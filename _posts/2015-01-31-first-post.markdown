---
layout: post
title:  "node js(express)로 google analytics 데이터 가져오기"
date:   2015-01-31 19:11:37
categories: node js,google anlytics
---

node js 를 이용해서 google analytics(ga) 데이터를 가져올때 주의점 

npm install googleanalytics 에서 제공하는 예제 파일 

{% highlight ruby %}

"use strict";

var ga = require('../lib/ga');

var profile='';
var username='ga 사용자 관리에서 추가한 이메일';
var password='이메일 패스워드';

var GA = new ga.GA({
  user: username,
  password: password
});

var dimensions = [
  'ga:date'
];

var metrics = [
  'ga:pageviews',
  'ga:visitors',
  'ga:transactions',
  'ga:transactionRevenue'
];

GA.login(function(err, token) {
  var options = {
    'ids': 'ga:'+profile,
    'start-date': '2012-07-01',
    'end-date': '2012-07-02',
    'dimensions': dimensions.join(','),
    'metrics': metrics.join(','),
    'sort': 'ga:date'
  };

  GA.get(options, function(err, entries) {
    if (!err) {
      console.log('date,pageviews,visitors,transactions,sales');
      entries.forEach(function(entry) {
        var buf = entry.dimensions[0]['ga:date'];

        metrics.forEach(function(metric) {
          buf += ',' + entry.metrics[0][metric];
        });

        console.log(buf);

      });
    } else {
      console.log(err);
    }
  });
});

{% endhighlight %}

내 경우에는 

<b>[Error: User does not have sufficient permissions for this profile.]</b> 

란 에러가 계속 났다

![ga_admin]({{ site.url }}/assets/ga_admin.png)


위에 이미지를 보면 카테고리가 계정, 속성, 보기로 나뉘는데 
그 중 "보기"에서 설정보기 메뉴로 들어가서 나오는 
"보기 ID"의 숫자 값을 

![ga_viewID]({{ site.url }}/assets/ga_viewID.png)

var profile= ""  << 요기에 넣어 주면 위와 같은 오류가 나지 않는다~

계정 ID를 넣으면 permissions 오류가 계속 난다. 


추가로 


1. BadAuthentication 오류

<b>
{ [Error: BadAuthentication
] responseData: 'Error=BadAuthentication\n' }
</b>

var username 에 ga 사용자 관리에서 지정한 이메일이 아닌 값이 들어가면 나오는 오류( 이메일의 앞 아디부분만 써도 마찬가지 오류가 남)

2. ga api 자료 

{% highlight ruby %}

var metrics = [
  'ga:pageviews',
  'ga:visitors',
  'ga:transactions',
  'ga:transactionRevenue'
];

{% endhighlight %}

요 부분에 들어가는 내가 가져오고 싶은 ga 데이터 값 api들은  



[Dimensions & Metrics Reference](https://developers.google.com/analytics/devguides/reporting/core/dimsmets)  요기서 확인 할수 있다. 


예제 파일에서 데이터 가져오기를 성공하면 

아래와 같이 터미널에서 가져온 데이터 값을 확인 할 수 있다. 

![result]({{ site.url }}/assets/result.png)




