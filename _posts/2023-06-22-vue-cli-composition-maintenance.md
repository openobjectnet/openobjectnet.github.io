---
title:  "프론트 팀원들에게 배운 Front 유지보수 기법(vue3 composition API 기준)"
search: false
categories: 
  - Vue
toc: true  
tags:
  - Vue
author: 허재원
---

# 프론트 팀원들에게 배운 Front 유지보수 기법(vue3 composition API 기준)

프론트 팀원들에게 배운 것 중 유지보수 관점으로 묶을 수 있는 것을 정리하였습니다. 여전히 부족한 점이 많으니 참고하여 읽어주시면 감사하겠습니다.

---

<br>

## <span style="color:#802548">_1. axios api를 관리하는 파일 만들기_</span>

<br>

- axios를 사용할 때, 일일이 각 method에서 직접 호출하는 식으로 사용했습니다.

```javascript
async function onFormSubmit() {
  const formData = new FormData();
  formData.append("boardTitle", boardTitle.value);
  formData.append("boardContent", boardContent.value);
  formData.append("boardWriter", boardWriter.value);

  await axios.post("/api/boards", formData);
}
```

- 위의 방식으로는 파일이 많아질수록 관리가 어렵기 때문에 따로 api를 모아놓는 파일을 만들어야 합니다.
- backend와 연동할 때 대부분 /api가 붙기 때문에 baseURL도 api를 붙인 상태로 설정했습니다.

```javascript
//instance.ts
export const instance = axios.create({
  timeout: 10 * 1000,
  baseURL: "http://localhost:8080/api",
});

//api.ts
export const boardInsert = (formData: FormData) => {
  instance.post("/boards", formData);
};

//*.vue
async function onFormSubmit() {
  const formData = new FormData();
  formData.append("boardTitle", boardTitle.value);
  formData.append("boardContent", boardContent.value);
  formData.append("boardWriter", boardWriter.value);
  await boardInsert(formData).then((response) => {
    alert("등록이 완료되었습니다.");
  });
}
```

- instance에서 url과 method 등을 구분해서 return하면 가독성이 더 좋아집니다.

```javascript
export const boardInsert = (formData: FormData) => {
  return instance({
    url: "/boards",
    method: "post",
    data: formData,
  });
};
```

<br>

## <span style="color:#802548">_2. 사용자 정의 interface 활용하기_</span>

<br>

- 위에서는 axios의 parameter type으로 FormData interface를 사용했습니다.
- 그보다 자신이 사용할 property를 지정하는 custom interface를 만들어서 활용하면 내용이 더 명확해집니다.
- 아래와 같이 새로운 interface와 class를 만들었습니다. interface를 지정했다면 class는 빈 생성자로 놔둬도 알아서 해당 요소를 갖는 class를 생성하게 한다고 합니다.

```javascript
export class Board {}
export interface Board {
  boardNo: number;
  boardTitle: string;
  boardContent: string;
  boardWriter: string;
  boardView: number;
  boardRegisterDate: Date;
}
```

- 이제는 FormData class를 활용하지 않고 직접 json object를 만들어 보내면 됩니다.

```javascript
async function onFormSubmit() {
  const boardInfo = {
    boardNo,
    boardTitle: boardTitle.value,
    boardContent: boardContent.value,
    boardWriter: boardWriter.value,
    boardView,
    boardRegisterDate,
  };
  await boardInsert(boardInfo).then((response) => {
    alert("등록이 완료되었습니다.");
  });
};

export const boardInsert = (formData: Board) =>{
    .
    .
    .
};
```

- 하지만 게시판을 등록할 때 등록날짜가 먼저 있을 수는 없습니다. 게시글 등록날짜는 게시판에 관한 정보를 response로 받아올 때 필요한 field입니다.
- 다시말해 request로 보내는 data와 response로 받는 data가 다르기에 따로 구분해서 만들어줍니다.

```javascript
//board.model.ts
export class BoardRequest {}
export interface BoardRequest {
  boardTitle: string;
  boardContent: string;
  boardWriter: string;
}

export class BoardResponse {}
export interface BoardResponse {
  boardNo: number;
  boardTitle: string;
  boardContent: string;
  boardWriter: string;
  boardView: number;
  boardRegisterDate: Date;
  boardPhoto: string;
}
```

- 그런데 게시판을 등록할 때 혹시 누군가는 내용을 넣고 싶지 않을수도 있습니다.
- 이와 같이 생략가능(optional) 속성은 ?를 통해 부여할 수 있습니다. optional이 되면 좀 더 유연하게 활용할 수 있습니다.

```javascript
export class BoardRequest {}
export interface BoardRequest {
  boardTitle: string;
  boardContent?: string;
  boardWriter: string;
}
```

- 다만 optional의 경우 값이 없거나 할당되지 않은 상태일 수 있습니다. 따라서 값이 있는지 확인하는 절차가 필요합니다.
- undefined나 null의 경우 falsy 값이기 때문에 조건문이 false가 되고 if문 안으로 들어가지 않게 됩니다.
- 이같이 if문을 통해서 적절한 값이 존재하는지 확인한 뒤 대입 처리를 해줄 수 있습니다.
- 그 외에도 optional chaining(?.), null coalescing(??) 등의 방법도 유효할 수 있습니다.

```javascript
if (boardContent) {
  boardContent.value = "value가 존재합니다";
}
```

- custom interface가 완성되었습니다.
- axios api가 받는 parameter의 type 또한 추상적인 formData interface가 아닌 구체적인 특성을 지닌 interface를 명시했습니다.
- boardContent의 경우 optional이라서 여기서는 optional chaining을 활용하였습니다.
- optional chaining은 optional property에 대해서만 적용해야 합니다.

```javascript
async function onFormSubmit() {
  const boardInfo = {
    boardTitle: boardTitle.value,
    boardContent: boardContent?.value,
    boardWriter: boardWriter.value,
  };

  await boardInsert(boardInfo).then((response) => {
    alert("등록이 완료되었습니다.");
  });
}

//api.ts
export const boardInsert = (boardInfo: Board) => {
  return instance({
    url: "/boards",
    method: "post",
    data: boardInfo,
  });
};
```

- 가독성을 해치는 then문을 없애주면 더 알아보기 쉬워지게 됩니다.

```javascript
async function onFormSubmit() {
  const boardInfo = {
    boardTitle: boardTitle.value,
    boardContent: boardContent?.value,
    boardWriter: boardWriter.value,
  };

  const statusCode = (await boardInsert(boardInfo)).status;
  if (statusCode === 200) {
    alert("등록이 완료되었습니다.");
  }
}
```

- 함수명도 더 직관적으로 와닿게 지어주고, 함수라는 것을 나타내기 위해 동사가 앞으로 오게 배치합니다.

```javascript
async function handleFormSave() {
  const boardInfo = {
    boardTitle: boardTitle.value,
    boardContent: boardContent?.value,
    boardWriter: boardWriter.value,
  };

  const statusCode = (await boardInsert(boardInfo)).status;
  if (statusCode === 200) {
    alert("등록이 완료되었습니다.");
  }
}
```

<br>

## <span style="color:#802548">_3. JS 내장 interface 활용하기_</span>

<br>

- typescript를 활용하기 이전에는 아래와 같이 간단하게 file 값을 가져올 수 있었습니다.

```javascript
function onFileChange($event) {
  fileData.value = $event.target.files[0];
}
```

- 하지만 typescript에서는 parameter의 type을 반드시 지정해야 합니다. 내장 interface 중 Event를 사용하면 됩니다.

```javascript
function onFileChange($event: Event) {
  fileData.value = event.target.files[0];
}
```

- Event의 target이라는 property는 EventTarget이라는 interface에 속합니다.
- 그런데 EventTarget에는 files라는 속성이 없습니다. files라는 property는 EventTarget에서 가져오는 게 아닙니다.
- files는 HTMLInputElement에서 가져오는 속성이라 HTMLInputElement에서 가져왔음을 명시해주어야 합니다.
- 명시해주는 방법으로 타입 단언이 있습니다. 아래와 같이 as를 붙여서 이것은 확실하게 이러한 type임을 선언해주는 행위입니다.

```javascript
const target = $event.target as HTMLInputElement;
  fileData.value = target.files[0];
```

- 하지만 위와 같이 쓰면 IDE에서 오류를 출력합니다. files 속성은 required가 아니라서 null일 가능성이 있기 때문입니다.
- 따라서 아래와 같이 값이 존재하는 경우에만 값을 대입하게 if문을 활용합니다.

```javascript
const target = $event.target as HTMLInputElement;
  if (target && target.files) {
    fileData.value = target.files[0];
  }
```

- optional chaining은 optional property에 대해서만 적용해야 한다고 하였습니다.
- HTMLImageElement의 src가 그 대표 사례입니다. src는 optional한 property가 아닌 required property입니다.
- 그러한 이유로 아래와 같이 optional chaining으로 값을 받아오려면 IDE에서 The left-hand side of an assignment expression may not be an optional property access라는 오류를 표시합니다.
- 이 오류는 우리를 괴롭히려는 게 아니라 runtime error를 줄여주기 위함입니다.

```javascript
  <img :src="'/upload/' + boardInfo.boardPhoto" ref="imagePreview" id="imagePreview"/>
  //이하 script
  const imagePreview = ref<HTMLImageElement>();
  const onFileChange = ($event: Event) => {
.
.
        imagePreview.value?.src = URL.createObjectURL(fileData.value);
      }
      // (document.getElementById('imagePreview') as HTMLImageElement).src = URL.createObjectURL(fileData.value);
```

- 따라서 아래와 같이 다시 쓸 수있습니다. optional property가 아닌 경우에는 if문으로 값이 있는 지 확인합니다.
- 또한 dom 객체를 직접 조작하기보단 vue의 ref 변수를 활용하는 것이 좋습니다.

```javascript
  <img :src="'/upload/' + boardInfo.boardPhoto" ref="imagePreview" />
  //이하 script
  const imagePreview = ref<HTMLImageElement>();
  const onFileChange = ($event: Event) => {
.
.
      if (imagePreview.value) {
        imagePreview.value.src = URL.createObjectURL(fileData.value);
      }
    };
```

- 만약 event가 esc와 같은 keyBoardEvent라면 어떨까요?

```javascript
function onEscapeKeydown($event) {
  if ($event.key === "Escape") {
    clearSeatItems();
  }
}
```

- 그럴 때는 Event interface가 아닌 keyboardEvent interface로 명시해주어야 합니다.

```javascript
function onEscapeKeydown($event: KeyboardEvent) {
  if ($event.key === "Escape") {
    clearSeatItems();
  }
}
```

<br>

## <span style="color:#802548">_4. component 분리 및 재사용하기_</span>

<br>

- 본래는 아래와 같이 기다란 component를 가지고 있었습니다.

```javascript
    <v-app id="inspire">
      <v-main class="blue-grey lighten-4">
        <v-container class="mt-5" style="max-width: 700px" fill-height>
          <v-card>
            <div class="pa-15">
              <h1 style="text-align: center" class="mb-10">세부조회 화면</h1>
              <v-row no-gutters>
                <v-col>
                  <v-sheet class="pa-2 ma-2">
                    <v-select label="출발역" :items="['수서', '동탄', '오송', '대전', '신경주', '울산', '부산', '공주', '익산', '목포']" v-model="scheduleInfo.scheduleDepartStation"></v-select>
                  </v-sheet>
                </v-col>
                <v-col>
                  <v-sheet class="pa-2 ma-2">
                    <v-select label="도착역" :items="['수서', '동탄', '오송', '대전', '신경주', '울산', '부산', '공주', '익산', '목포']" v-model="scheduleInfo.scheduleArriveStation"></v-select>
                  </v-sheet>
                </v-col>
              </v-row>
              <v-row no-gutters>
                <v-col>
                  <v-sheet class="pa-2 ma-2">
                    <v-text-field label="날짜" v-model="scheduleInfo.scheduleDate"></v-text-field>
                  </v-sheet>
                </v-col>
                <v-col>
                  <v-sheet class="pa-2 ma-2">
                    <v-select label="시간" :items="['00시 이후', '04시 이후', '08시 이후', '12시 이후', '16시 이후', '20시 이후']" v-model="scheduleInfo.scheduleTime"></v-select>
                  </v-sheet>
                </v-col>
              </v-row>
              <v-row no-gutters>
                <v-col>
                  <v-sheet class="pa-2 ma-2">
                    <v-select label="성인 사람 수" :items="[1, 2, 3]" v-model="headcountInfo.headCountAdult"></v-select>
                  </v-sheet>
                </v-col>
                <v-col>
                  <v-sheet class="pa-2 ma-2">
                    <v-select label="어린이 사람 수" :items="[1, 2, 3]" v-model="headcountInfo.headCountChild"></v-select>
                  </v-sheet>
                </v-col>
                <v-col>
                  <v-sheet class="pa-2 ma-2">
                    <v-select label="노인 사람 수" :items="[1, 2, 3]" v-model="headcountInfo.headCountElder"></v-select>
                  </v-sheet>
                </v-col>
                <v-col>
                  <v-sheet class="pa-2 ma-2">
                    <v-select label="중증 사람 수" :items="[1, 2, 3]" v-model="headcountInfo.headCountSevere"></v-select>
                  </v-sheet>
                </v-col>
              </v-row>
            </div>
            <v-btn type="button" color="blue lighten-1 text-capitalize" depressed large block dark class="mb-3" @click="handleFetchSchedule"> 조회하기 </v-btn>
          </v-card>
        </v-container>
      </v-main>

      <v-table>
        <tr>
          <td>출발역</td>
          <td>도착역</td>
          <td>출발시간</td>
          <td>도착시간</td>
          <td>기차번호</td>
        </tr>
        <tr v-for="schedule in scheduleList" :key="schedule.scheduleNo">
          <td>{{ schedule.departStation.stationName }}</td>
          <td>{{ schedule.arriveStation.stationName }}</td>
          <td>{{ schedule.scheduleDepartDatetime }}</td>
          <td>{{ schedule.scheduleArriveDatetime }}</td>
          <td>{{ schedule.scheduleTrainNo }}</td>
        </tr>
      </v-table>
    </v-app>
```

- 하지만 길어서 보기 불편합니다. 분리하고 싶습니다.
- 일정의 필터링 조건과, 필터링 조건에 따른 일정 결과로 분리될 수 있을 거 같습니다.
- 아래서 테이블은 조건을 결정한 이후에 조회하기를 누르면 일정 결과를 보여주는 component입니다.

```javascript
  <v-table>
    <tr>
      <td>출발역</td>
      <td>도착역</td>
      <td>출발시간</td>
      <td>도착시간</td>
      <td>기차번호</td>
      <td>특실</td>
      <td>일반실</td>
    </tr>
    <tr v-for="schedule in scheduleList" :key="schedule.scheduleNo">
      <td>{{ schedule.departStation }}</td>
      <td>{{ schedule.arriveStation }}</td>
      <td>{{ schedule.scheduleDepartDatetime }}</td>
      <td>{{ schedule.scheduleArriveDatetime }}</td>
      <td>{{ schedule.scheduleTrainNo }}</td>
    </tr>
  </v-table>
```

- component는 분리했습니다. 그러나 일정의 필터링 조건 component는 여전히 반복되는 것들이 많습니다.

```javascript
    <v-app id="inspire">
      <v-main class="blue-grey lighten-4">
        <v-container class="mt-5" style="max-width: 700px" fill-height>
          <v-card>
            <div class="pa-15">
              <h1 style="text-align: center" class="mb-10">세부조회 화면</h1>
              <v-row no-gutters>
                <v-col>
                  <v-sheet class="pa-2 ma-2">
                    <v-select label="출발역" :items="['수서', '동탄', '오송', '대전', '신경주', '울산', '부산', '공주', '익산', '목포']" v-model="scheduleInfo.scheduleDepartStation"></v-select>
                  </v-sheet>
                </v-col>
                <v-col>
                  <v-sheet class="pa-2 ma-2">
                    <v-select label="도착역" :items="['수서', '동탄', '오송', '대전', '신경주', '울산', '부산', '공주', '익산', '목포']" v-model="scheduleInfo.scheduleArriveStation"></v-select>
                  </v-sheet>
                </v-col>
              </v-row>
              <v-row no-gutters>
                <v-col>
                  <v-sheet class="pa-2 ma-2">
                    <v-text-field label="날짜" v-model="scheduleInfo.scheduleDate"></v-text-field>
                  </v-sheet>
                </v-col>
                <v-col>
                  <v-sheet class="pa-2 ma-2">
                    <v-select label="시간" :items="['00시 이후', '04시 이후', '08시 이후', '12시 이후', '16시 이후', '20시 이후']" v-model="scheduleInfo.scheduleTime"></v-select>
                  </v-sheet>
                </v-col>
              </v-row>
              <v-row no-gutters>
                <v-col>
                  <v-sheet class="pa-2 ma-2">
                    <v-select label="성인 사람 수" :items="[1, 2, 3]" v-model="headcountInfo.headCountAdult"></v-select>
                  </v-sheet>
                </v-col>
                <v-col>
                  <v-sheet class="pa-2 ma-2">
                    <v-select label="어린이 사람 수" :items="[1, 2, 3]" v-model="headcountInfo.headCountChild"></v-select>
                  </v-sheet>
                </v-col>
                <v-col>
                  <v-sheet class="pa-2 ma-2">
                    <v-select label="노인 사람 수" :items="[1, 2, 3]" v-model="headcountInfo.headCountElder"></v-select>
                  </v-sheet>
                </v-col>
                <v-col>
                  <v-sheet class="pa-2 ma-2">
                    <v-select label="중증 사람 수" :items="[1, 2, 3]" v-model="headcountInfo.headCountSevere"></v-select>
                  </v-sheet>
                </v-col>
              </v-row>
            </div>
            <v-btn type="button" color="blue lighten-1 text-capitalize" depressed large block dark class="mb-3" @click="handleFetchSchedule"> 조회하기 </v-btn>
          </v-card>
        </v-container>
      </v-main>
```

## <span style="color:#802548">_5. v-for를 활용해 반복되는 template 코드 간소화하기_</span>

<br>

- 위의 코드에서 단순 반복을 줄이기 위해 v-for와 배열을 활용합니다.
- 우선 역을 선택하는 조건을 간소화합니다. 반복되는 요소와 반복되지 않는 요소를 나눕니다.
- 우선 select의 내용은 아래와 같이 빼낼 수 있습니다.

```javascript
const stationItems = [
  "수서",
  "동탄",
  "오송",
  "대전",
  "신경주",
  "울산",
  "부산",
  "공주",
  "익산",
  "목포",
];
```

- 그 외에는 label과 v-model 값은 변화하고 나머지는 반복되는 양상을 보입니다. 따라서 아래와 같이 변주되는 부분만을 잡아줄 배열을 만듭니다.

```javascript
const stationFilteringItems = [
  { label: "출발역", value: "scheduleDepartStation" },
  { label: "도착역", value: "scheduleArriveStation" },
];
```

- 그럼 아래와 같이 간결하게 쓸 수 있습니다.

```javascript
    <v-app id="inspire">
      <v-main class="blue-grey lighten-4">
        <v-container class="mt-5" style="max-width: 700px" fill-height>
          <v-card>
            <div class="pa-15">
              <h1 style="text-align: center" class="mb-10">세부조회 화면</h1>
              <v-row no-gutters>
                <v-col>
                  <v-sheet class="pa-2 ma-2">
                    <v-select v-for="element in stationFilteringItems" :label="element.label" :key="element.label" :items="stationItems" v-model="scheduleInfo[element.value]"></v-select>
                  </v-sheet>
                </v-col>
              </v-row>
.
.
.
```

- 여기서 한 가지 더 추가할 사항이 있습니다. property의 value를 대괄호로 꺼내서 가져오려면 index signature가 필요합니다. 예를 들어 아래가 기존의 interface라고 해보겠습니다.

```javascript
export class ScheduleRequest {}
export interface ScheduleRequest {
  scheduleNo?: number;
  scheduleDepartStation: string;
  scheduleArriveStation: string;
  scheduleDate: string;
  scheduleTime: string;
}
```

- 하지만 위와 같은 형태로는 대괄호 표기법을 사용할 수 없습니다. 따라서 아래와 같은 내용이 interface에 추가됩니다.
- 대괄호안에 string type의 key가 들어가면 property의 key에 대응되는 value를 return합니다.
- index signature는 아무 type의 key나 들어가지 못하게 type을 지정하여 안정성을 더한 type script의 방식입니다.
- 이 때 return type은 모든 property의 value type을 포괄할 수 있어야 합니다.

```javascript
export interface ScheduleRequest {
.
.
[key: string]: string | number | undefined;
}
```


- 그럼 마저 아래도 짧게 만들어 줍니다.

```javascript
 <v-row no-gutters>
      <v-col>
        <v-sheet class="pa-2 ma-2">
          <v-select label="성인 사람 수" :items="[1, 2, 3]" v-model="headcountInfo.headCountAdult"></v-select>
        </v-sheet>
      </v-col>
      <v-col>
        <v-sheet class="pa-2 ma-2">
          <v-select label="어린이 사람 수" :items="[1, 2, 3]" v-model="headcountInfo.headCountChild"></v-select>
        </v-sheet>
      </v-col>
      <v-col>
        <v-sheet class="pa-2 ma-2">
          <v-select label="노인 사람 수" :items="[1, 2, 3]" v-model="headcountInfo.headCountElder"></v-select>
        </v-sheet>
      </v-col>
      <v-col>
        <v-sheet class="pa-2 ma-2">
          <v-select label="중증 사람 수" :items="[1, 2, 3]" v-model="headcountInfo.headCountSevere"></v-select>
        </v-sheet>
      </v-col>
  </v-row>
```

- 위의 부분에서도 반복되는 부분은 label과 v-model입니다. 따라서 아래와 같이 v-for에서 순회할 배열을 만들어줍니다.

```javascript
const headcountFilteringItems = [
  { label: "성인 사람 수", value: "headCountAdult" },
  { label: "어린이 사람 수", value: "headCountChild" },
  { label: "노인 사람 수", value: "headCountElder" },
  { label: "중증 사람 수", value: "headCountSevere" },
];
```

- 그럼 아래와 같이 짧은 template으로 위의 긴 내용을 대체할 수 있습니다.

```javascript
<v-row no-gutters>
                <v-col>
                  <v-sheet class="pa-2 ma-2">
                    <v-select v-for="(element, i) in headcountFilteringItems" :key="i" :label="element.label" :items="[1, 2, 3]" v-model="headcountInfo[element.value]"></v-select>
                  </v-sheet>
                </v-col>
              </v-row>
```

- 그럼 다시 index signature를 생성해줍시다.
- 여기서는 모든 property의 value가 number type이기 때문에 union type으로 string type을 포함하지 않아도 됩니다.

```javascript
export class HeadCount {}
export interface HeadCount {
  headCountAdult: number;
  headCountChild: number;
  headCountSevere?: number;
  headCountElder?: number;
  [key: string]: number | undefined;
}
```

- 그럼 최종적으로 아래와 같은 좀 더 간결해진 component가 만들어집니다.

```javascript
<v-app id="inspire">
      <v-main class="blue-grey lighten-4">
        <v-container class="mt-5" style="max-width: 700px" fill-height>
          <v-card>
            <div class="pa-15">
              <h1 style="text-align: center" class="mb-10">세부조회 화면</h1>
              <v-row no-gutters>
                <v-col>
                  <v-sheet class="pa-2 ma-2">
                    <v-select v-for="element in stationFilteringItems" :label="element.label" :key="element.label" :items="stationItems" v-model="scheduleInfo[element.value]"></v-select>
                  </v-sheet>
                </v-col>
              </v-row>
              <v-row no-gutters>
                <v-col>
                  <v-sheet class="pa-2 ma-2">
                    <v-text-field label="날짜" v-model="scheduleInfo.scheduleDate"></v-text-field>
                  </v-sheet>
                </v-col>
                <v-col>
                  <v-sheet class="pa-2 ma-2">
                    <v-select label="시간" :items="timeItems" v-model="scheduleInfo.scheduleTime"></v-select>
                  </v-sheet>
                </v-col>
              </v-row>
              <v-row no-gutters>
                <v-col>
                  <v-sheet class="pa-2 ma-2">
                    <v-select v-for="(element, i) in headcountFilteringItems" :key="i" :label="element.label" :items="[1, 2, 3]" v-model="headcountInfo[element.value]"></v-select>
                  </v-sheet>
                </v-col>
              </v-row>
            </div>
            <v-btn type="button" color="blue lighten-1 text-capitalize" depressed large block dark class="mb-3" @click="handleFetchSchedule()"> 조회하기 </v-btn>
          </v-card>
        </v-container>
      </v-main>

      <schedule-result  />
    </v-app>
```

- 위에서 table을 따로 분리한 component를 사용하려면 아래와 같이 component를 components scope에 선언하고 import해야 합니다.
- 그럼 위에서 tag안에 담긴 schedule-result와 같이 kebab case로 사용가능합니다.

```javascript
import ScheduleResult from '@/views/schedule/ScheduleResult.vue';
export default defineComponent({
  name: 'ScheduleView',
  setup() {
.
.
.
  },
  components: { ScheduleResult },
});
```

## <span style="color:#802548">_6. props를 활용하기_</span>

<br>

- 위에서 분리했던 table component에서 scheduleList라는 것을 활용하고 있습니다.
- 이를 store에 저장해서 불러올 수도 있지만, 부모-자식 관계를 활용할 수도 있습니다.

```javascript
  <v-table>
    <tr>
      <td>출발역</td>
      <td>도착역</td>
      <td>출발시간</td>
      <td>도착시간</td>
      <td>기차번호</td>
      <td>특실</td>
      <td>일반실</td>
    </tr>
    <tr v-for="schedule in scheduleList" :key="schedule.scheduleNo">
      <td>{{ schedule.departStation }}</td>
      <td>{{ schedule.arriveStation }}</td>
      <td>{{ schedule.scheduleDepartDatetime }}</td>
      <td>{{ schedule.scheduleArriveDatetime }}</td>
      <td>{{ schedule.scheduleTrainNo }}</td>
    </tr>
  </v-table>
```

- setup()에서는 아래와 같이 props를 선언하여 활용할 수 있습니다.
- 사용자가 정의한 custom interface의 경우 아래와 같이 PropType으로 type을 단언(assertion)해줍니다.
- 이를 통해 해당 변수는 props라서 readonly인 것을 더 명확하게 인지할 수 있습니다.
- 아래 required 속성을 true로 설정하지 않으면 props가 undefined일 경우를 대비한 처리가 필요합니다.

```javascript
export default defineComponent({
  name: 'ScheduleResult',
  props: {
    scheduleList: {
      type: Object as PropType<Array<ScheduleResponse>>,
      required: true,
    },
    headcountInfo: {
      type: Object as PropType<HeadCount>,
      required: true,
    },
  },
    setup() {
      .
      .
      .
      },
      components:{
        .
        .
      }
})
```

- template에서만 쓸 예정이라면 setup 안으로 가져올 필요가 없습니다.
- 하지만 script에서 변수로 쓸 생각도 있다면 setup() 안에 매개변수로 props를 적고 꺼내 써야 합니다.
- props 값을 변경하기 보다는 자식 component에서 props를 활용해 새로운 변수를 만드는 것이 권장됩니다.

```javascript
    setup(props) {
       let sumHeadCount = props.headcountInfo.headCountAdult + props.headcountInfo.headCountChild + (props.headcountInfo.headCountElder ?? 0);
      .
      .
      .
      onBeforeUpdate(() => {
        sumHeadCount = props.headcountInfo.headCountAdult + props.headcountInfo.headCountChild + (props.headcountInfo.headCountElder ?? 0);
      });
    },
```

- child component에서 props를 선언했으므로 이제 parent component에서 child component로 props를 보낼 수 있습니다.

```javascript
<v-app id="inspire">
      <v-main class="blue-grey lighten-4">
        <v-container class="mt-5" style="max-width: 700px" fill-height>
          <v-card>
            <div class="pa-15">
              <h1 style="text-align: center" class="mb-10">세부조회 화면</h1>
                  .
                  .
                  .
   <schedule-result :scheduleList="scheduleList" :headcountInfo="headcountInfo" />
```

- headcountInfo라는 props는 script에서, scheduleList라는 props는 template에서 활용되고 있습니다.
- template의 경우 props.headcountInfo처럼 property에 접근하지 않아도 바로 활용할 수 있습니다.
- headCountElder라는 property가 optional이라서 null-coalescing을 활용하였습니다.

```javascript
  <v-table>
    <tr>
      <td>출발역</td>
      <td>도착역</td>
      <td>출발시간</td>
      <td>도착시간</td>
      <td>기차번호</td>
      <td>특실</td>
      <td>일반실</td>
    </tr>
    <tr v-for="schedule in scheduleList" :key="schedule.scheduleNo">
      <td>{{ schedule.departStation }}</td>
      <td>{{ schedule.arriveStation }}</td>
      <td>{{ schedule.scheduleDepartDatetime }}</td>
      <td>{{ schedule.scheduleArriveDatetime }}</td>
      <td>{{ schedule.scheduleTrainNo }}</td>
    </tr>
  </v-table>

//아래는 script
  export default defineComponent({
  name: 'ScheduleResult',
  props: {
    scheduleList: {
      type: Object as PropType<Array<ScheduleResponse>>,
      required: true,
    },
    headcountInfo: {
      type: Object as PropType<HeadCount>,
      required: true,
    },
  },

  setup(props) {
    let sumHeadCount = props.headcountInfo.headCountAdult + props.headcountInfo.headCountChild + (props.headcountInfo.headCountElder ?? 0);
  .
  .

    onBeforeUpdate(() => {
      sumHeadCount = props.headcountInfo.headCountAdult + props.headcountInfo.headCountChild + (props.headcountInfo.headCountElder ?? 0);
    });
    .
    .
  },
});
```
