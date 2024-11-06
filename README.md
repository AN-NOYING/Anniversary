# 기념일

정부에서 지정한 대한민국의 53종의 기념일과 일부 전세계 공용 기념일이 작성된 `json` 포맷의 파일입니다.

대한민국 지정 기념일은 [행정안전부](https://www.mois.go.kr/chd/sub/a05/feteDay/screen.do)의 자료를 참고하였습니다.

## 목차

* [기념일](#기념일)
* [데이터 구조](#데이터-구조)
  * [date의 구조](#date의-구조)
  * [relevant_deparment의 구조](#relevant_department의-구조)
* [사용법](#사용법)
  * [CDN](#CDN)
  * [javascript](#javascript)

----------

# 데이터 구조

|변수|의미|데이터 형식|
|:-----|:-----|:-----|
|`title`|기념일의 이름|`String`|
|`date`|날짜 정보|`Object`|
|`relevant_department`|유관 부서|`Array`|
|`desc`|기념일의 설명|`String`|

## `date`의 구조

|변수|의미|데이터 형식|데아터 설명|
|:-----|:-----|:-----|:-----|
|`isSpecificDay`|특정 날짜 플래그|`Boolean`|`true`, `false`|
|`month`|월(月)|`Number`|1-12|
|`week`|주(週)|`Number`|-1 또는 1-4|
|`day`|일(日)|`Number`|-1 또는 1-31|
|`weekday`|요일(曜日)|`Number`|-1 또는 0-6|

`isSpecificDay`는 특별히 정해지지 않은 날짜를 찾을 때 사용합니다. (예: 10월 **셋째 토요일**)

`isSpecificDay`가 `true`의 값을 가지면 `week`와 `weekday`는 -1의 값을 가집니다. 즉, 정해진 날이기 때문에 날짜를 찾을 필요가 없습니다.

`week`의 값이 4 이상이면 마지막 주차로 판단합니다.

## `relevant_department`의 구조

`relevant_department`는 유관 부서(관련 부서)를 의미하며, 문자열 배열로 이루어져 있습니다.

현 json 파일에 존재하는 부서는 아래 표와 같습니다.

|부서|
|:-----|
|국가보훈처|
|기획재정부|
|산업통상자원부|
|행정안전부|
|국방부|
|농림축산식품부|
|보건복지부|
|과학기술정보통신부|
|방송통신위원회|
|법무부|
|문화체육관광부|
|고용노동부|
|교육부|
|여성가족부|
|해양수산부|
|환경부|
|국토교통부|
|국가정보원|
|외교부|
|금융위원회|
|공정거래위원회|
|산업통상자원부|

# 사용법
## CDN
```javascript
const cdnUrl = "https://cdn.jsdelivr.net/gh/AN-NOYING/Anniversary/south-korea-anniversary.json";
````
행정안전부 기준 53종의 기념일만 작성되어 있는 json 파일입니다.
<br><br>
```javascript
const cdnUrl = "https://cdn.jsdelivr.net/gh/AN-NOYING/Anniversary/south-korea-and-global-anniversary.json";
````
행정안전부 기준 53종의 기념일과 일부 공용 기념일이 작성되어 있는 json 파일입니다.

## javascript
```javascript
document.addEventListener('DOMContentLoaded', () => {
  fetch("https://cdn.jsdelivr.net/gh/AN-NOYING/Anniversary/south-korea-anniversary.json")
  .then((response) => {  
    if (response.ok) { 
      return response.json(); 
    } else { 
      console.log('Error: Failed to load data'); 
    }
  })
  .then((data) => {
    const today = new Date('2024-11-03'); // 임시로 특정 날짜 설정

    data.forEach((item) => {
      if (item.date.isSpecificDay) {
        if (item.date.month === today.getMonth() + 1 && item.date.day === today.getDate()) {
          console.log(`${item.title}입니다.`);
        }
      } else {
        // 마지막 주인지 판단
        const isLastWeek = (month, week) => {
          // 주차가 4 이상이면 마지막 주로 간주
          return week >= 4;
        };

        // 날짜 계싼 후 취득
        const getDateFromWeekAndWeekday = (month, week, weekday) => {
          const year = today.getFullYear();
          const firstDayOfMonth = new Date(year, month - 1, 1);
          const lastDayOfMonth = new Date(year, month, 0);

          // 첫 번째 주차의 해당 요일 찾기
          const firstWeekdayDate = firstDayOfMonth.getDate() + ((7 + weekday - firstDayOfMonth.getDay()) % 7);
          
          // 마지막 주차의 해당 요일 찾기
          const lastWeekdayDate = lastDayOfMonth.getDate() - ((lastDayOfMonth.getDay() - weekday + 7) % 7);

          if (isLastWeek(month, week)) {
            return new Date(year, month - 1, lastWeekdayDate); // 마지막 주의 해당 요일
          } else {
            const targetDate = firstWeekdayDate + (week - 1) * 7; // 첫 번째 해당 요일에서 주차만큼 더한 날짜
            return new Date(year, month - 1, targetDate);
          }
        };

        const calculatedDate = getDateFromWeekAndWeekday(item.date.month, item.date.week, item.date.weekday);
        if (calculatedDate.getTime() === today.getTime()) {
          console.log(`${item.title}입니다.`);
        }
      }
    });
  })
  .catch((err) => {
    console.log(err);
  });
});
```
```
학생독립운동 기념일입니다.
```
