---
title:  "오픈오브젝트 멘토링제도 수행기"
last_modified_at: 2020-05-19T16:01:04-04:00
categories: 
  - Experience
tags:
  - update
toc: true
toc_label: "시기별"
---

우리회사에 멘트링 제도가 생겼다.  
멘토링 제도는 신입개발자가 들어오면 신입개발자가 적응을 도우고, 업무에 잘 적응할수 있도록 하는 제도.
멘토링을 진행하면서 있었던 이야기와 진행한 내용을 남깁니다

멘토링을 진행하면 간단한 프로젝트를 진행하며 개발에 대한 피드백을 진행할 예정이다.  

프로젝트 환경  
- server : aws + tomcat  
- server framework : spring  
- db : maria DB  
- css framework : bootstrap  
- 서버배포 도구 : jenkins  

## 1-1 주차 : 신입 개발자와 멘토링 시작
**오리엔테이션**  
- 프로젝트에 투입이 되면 누구와 어떤일을 할까?
- 파견 사이트별 특징(card사 or 그외)  
- 프로젝트의 진행과정  
- 서버개발자가 프로젝트에서 해야할일. feat.서버개발자 시점  


**채택 서적 : 열혈강의 자바 웹 개발 워크북**  
채택 이유 : 웹개발에 대해서 알아야 할내용은 끝이 없다. 이 책에는 개발을 진행할떄, 필수적으로 알아야할 내용을 실습을 통해 설명해준다.  
해당 책은 웹에대한 설명과 spring + mybatis 실습을 할수 있게 구성되어 있다.  
금융권 SI는 시스템에 대해 안정성을 추구 하고, 보수적인 편이라서 개발의 비교적 새로운 기술들(string boot, jpa, vue 등) 을 진행하기보다 보편적인 개발 기술로 정하였다. 

## 1-2 주차 : DashBoard 프로젝트 생성

`TODO`

Replace `gem "github-pages` or `gem "jekyll"` with `gem "jekyll", "~> 3.3.0"`. You'll need the latest version of Jekyll[^update-jekyll] for Minimal Mistakes to work and load all of the /assets/ properly.

[^update-jekyll]: You could also run `bundle update jekyll` to update Jekyll.

Add the Minimal Mistakes theme gem: 

```ruby
gem "minimal-mistakes-jekyll"
```

When finished your `Gemfile` should look something like this:

```ruby
source "https://rubygems.org"

gem "jekyll", "~> 3.3.0"
gem "minimal-mistakes-jekyll"
```

## Step 3: Run Bundler

Run `bundle install` (or `bundle update` if you're updating an existing repo) to install/update Jekyll and the theme.

## Step 4: Install the Theme

Add `theme: "minimal-mistakes-jekyll"` to your `_config.yml` file.

If you're migrating from an existing Minimal Mistakes site you shouldn't have to change anything else after this. If it's a new site consult then docs to [properly config]({{ "/docs/configuration/" | relative_url }}).

**Please Note:** Paths for image headers, overlays, teasers, [galleries]({{ "/docs/helpers/#gallery" | relative_url }}), and [feature rows]({{ "/docs/helpers/#feature-row" | relative_url }}) have changed and now require a full path. Instead of just `image: filename.jpg` you'll need to use the full path eg: `image: assets/images/filename.jpg`. The preferred location is now `assets/images` but can be placed elsewhere or external hosted. This all applies for image references in `_config.yml` and `author.yml` as well.
{: .notice--danger}

## Step 5: `jekyll new` Tweaks

If this is a new site be sure to add the following files to `_data/` and customize as you see fit. There is currently no way of bundling them in with the theme, so be sure to consult the docs on how to properly use both.

- [`_data/ui-text.yml`](https://github.com/mmistakes/minimal-mistakes/blob/master/_data/ui-text.yml) - UI text [documentation]({{ "/docs/ui-text/" | relative_url }})
- [`_data/navigation.yml`](https://github.com/mmistakes/minimal-mistakes/blob/master/_data/navigation.yml) - navigation [documentation]({{ "/docs/navigation/" | relative_url }})

You'll also need to: 

- Replace `<site root>/index.html` with a modified [Minimal Mistakes `index.html`](https://github.com/mmistakes/minimal-mistakes/blob/master/index.html).
- Change `layout: post` in `_posts/0000-00-00-welcome-to-jekyll.markdown` to `layout: single`.
- Remove `about.md`, or at the very least change `layout: page` to `layout: single` and remove references to `icon-github.html` (or [copy to your `_includes`](https://github.com/jekyll/minima/tree/master/_includes) if using).

---

That's it! If all goes well running `bundle exec jekyll serve` should spin-up your site. If you encounter any bumps please file an issue on GitHub and make sure to indicate you're testing the pre-release Ruby gem version.

[File an issue](https://github.com/mmistakes/minimal-mistakes/issues/new){: .btn .btn--info .btn--large}

Thanks!
