## day.js

<details>
<summary>1. ISO-8601에 따른 보고서 만들기</summary>
[#이미지삽입 - 카톡]

  [ISO-8601](https://ko.wikipedia.org/wiki/ISO_8601)란 간략하게
  날짜와 시간을 표현함에 있어서 명백하고 잘 정의된 방법을 제공하고자 함에 그 목적이 있다.  
  <br/><br/>월별 주차를 표현함에 있어서 다음과 같은 규칙을 표함한다. 
  <br/><br/> (1) 각 월의 첫주차는 목요일을 포함하여, 최소 4일이 있어야 한다. 
  <br/><br/> (2) 이때 첫주의 시작일은 '월요일'로 한다. (일요일이 아니다.)
  <br/><br/> (3) 마지막 주의 경우 역시 4일을 포함으로 하기에, 목요일을 포함해야 한다. 
  
  <br/><br/> 이러한 사례를 따를 때, 23-12-01은 12월의 첫주차가 아니고, 11월의 마지막 주가 되어야 한다. 
  <br/><br/> 이를 충족하는 프로젝트를 구현함에 있어서, 넥스트+타입스크립트+day.js 를 활용하여 진행하였다. 

  ```javascript
  import dayjs, { Dayjs } from 'dayjs';
  // dayjs plugin 사용을 위한 import 구문
  import isoWeek from 'dayjs/plugin/isoWeek';
  import weekOfYear from 'dayjs/plugin/weekOfYear';

  // 플러그인의 선언을 커스컴훅으로 관리하기 위해 useDayjsPlugins를 작성
  export const useDayjsPlugins = () => {
    dayjs.extend(weekOfYear);
    dayjs.extend(isoWeek);
    // dayjs-iso 메서드의 시작일은 "월요일"
    const ISO8601 = dayjs().isoWeekday(4).format('YYYY-MM-DD');
    return { ISO8601 };
  };

  // 사용할 컴포넌트에서 선언
  export function ReportIndex() {
    const { ISO8601 } = Hooks.useDayjsPlugins();
    
    const onCalcWeeks = (data: string) => {
      console.log('=============================');
      const monthStart = dayjs(data).startOf('month');
      const monthEnd = dayjs(data).endOf('month');
      const startDayOfWeek = monthStart.day();
      const endDayOfWeek = monthEnd.day();
      const monthStartIsoWeek =
        !!startDayOfWeek && startDayOfWeek < 5
          ? monthStart.isoWeek()
          : monthStart.add(1, 'week').isoWeek();
      const monthLastIsoWeek =
        !!endDayOfWeek && endDayOfWeek < 4
          ? monthEnd.subtract(1, 'week').isoWeek()
          : monthEnd.isoWeek();
      const amountMonthWeeks = monthLastIsoWeek - monthStartIsoWeek + 1;
      console.log(monthStart.format('MM월'));
      console.log('monthStartIsoWeek', monthStartIsoWeek);
      console.log('monthLastIsoWeek', monthLastIsoWeek);
      console.log('amountMonthWeeks', amountMonthWeeks);
    };
  
    onCalcWeeks(data);

    return <div />
  }
  ```
  <br/><br/> 테스트를 위해 코드를 분리하지 않았지만, 위와 같다. 
  <br/><br/> (1) onCalcWeeks(data)를 통해 입력된 날짜에 따른 월별 주차를 계산한다. 
  <br/><br/> (2) 코드에서는 제거되었지만, data는 버튼에 의해 전달과 다음달로 이동한다. 
  <br/><br/> (3) 시작주차의 계산을 위해서 `monthStart`를 구하고 난 후에 조건분기에 따라, 목요일을 기점으로 `monthStartIsoWeek`를 계산하였다.
  <br/><br/> (4) 마지막주차의 계산을 위해서 `monthEnd`를 구하고 난 후에 조건분기에 따라, 목요일을 기점으로 `monthLastIsoWeek`를 계산하였다.
  <br/><br/> (5-1) 이때 계산을 조절하는 핵심은 startDayOfWeek와 endDayOfWeek인데, 월(1)~토(6)~일(0)로 계산하여야 한다.
  <br/><br/> (5-2) 이를 위해, 1부터 4까지의 요일값으로  monthStartIsoWeek를 계산하고, 1부터 3까지의 요일값으로 monthLastIsoWeek를 계산하였다.
  <br/><br/> (6-1) 마지막으로 `amountMonthWeeks`를 계산하여 해당 월이 몇주차까지 있는지 계산함으로 sideNav에 주차별 보고서 탭이 생성되게 하였으며
  <br/><br/> (6-2) 현재월을 제외하고는 모든 주차가 표시, 현재월은 현재월의 진행주차에 따라 주차별 보고서 탭이 생성되게 하였다. 
</details>
