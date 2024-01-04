## Chart.js
[npmtrends](https://npmtrends.com/chart.js-vs-plotly.js-vs-react-chartjs-2-vs-recharts)를 기준으로 볼 때, 그래프와 관련된 라이브러리에서 단연 으뜸은 `Chart.js`라이브러리일 것이다. 
<p align="center"><img src='./chartjs001.png' width="500"></p>

`Chart.js`는 다양한 도표들을 그릴 수 있는데 관련된 내용은 공식 문서를 참고하면 쉽게 구현할 수 있다. 

<details>
  <summary>Options Settings</summary>

  ---
  ```javascript
  // 리액트(넥스트) 타입스크립트에서 해당 라이브러리를 사용하기 위해서는 아래와 같이 기본적으로 선언해야 하는 값들이 있다. 
  import {
    Chart as ChartJS,
    CategoryScale,
    LinearScale,
    PointElement,
    LineElement,
    Title,
    Tooltip,
    Legend,
  } from 'chart.js';
  import { Line } from 'react-chartjs-2';

  ChartJS.register(
    CategoryScale,
    LinearScale,
    PointElement,
    LineElement,
    Title,
    Tooltip,
    Legend
  );

  const options = {
      responsive: boolean,
      maintainAspectRatio: boolean,
      interaction: {},
      plugins: {
        legend: {},
        tooltip: {},
      }, 
      scales: {
        x: {},
        y: {},
      },
      elements: {
        point: {},
        line: {
          tension: 0.2,
        },
      },
    };
  ```
  - `responsive` : 반응형으로 그래프를 동작하게 할 것인지를 설정한다. 그러나 해당설정은 window.resize 되었을 때, 그래프가 줄어들며 살아지는 효과를 주기도 하기에, false로 설정값을 변경하자.
    - `maintainAspectRatio` : 는 그래프의 종횡비를 유지할 것인지에 대한 설정값으로, true를 설정하면, 가로세로 비율이 유지된다. 반면, false로 설정하면, 가능한 공간에 따라 차트가 늘어나거나 줄어들게 된다.
    - `interaction` : 차트와 사용자 간의 상호작용에 대한 부분을 설정한다. 해당 설정으로 호버시 보여질 범례의 갯수를 제어할 수 있다. 전체를 보여주기 위해서는 `mode: 'index' as const`를 해당 단 건을 보여주기 위해서는 `mode: 'nearest' as const`를 설정하면 된다. 또한, 근처로만 가도 동작하게 할지, 말지에 대해서는 `intersect: false`를 통해 조절할 수 있다.
    - `plugins` : plugins를 통해서 범례 및 튤팁에 대해서 제어할 수 있다.
    - `scales` : scales은 y축과 x축에 대한 설정을 기록할 수 있다.
    - `elements` 로 해당 point에 대해 설정을 기본시, 호버시로 설정할 수 있으며, line을 통해서 라인의 곡률을 지정할 수 있다.
   
  튤팁 부분만 따로 더 이야기 하자면 아래와 같다. 
  ```javascript
  tooltip: {
        backgroundColor: (context: any) =>
          context.tooltip.dataPoints[0].dataset.backgroundColor,
          // context 객체로부터 각 라인의 backgroundColor을 얻어와서 반영할 수 있다. 
        padding: 10,
        cornerRadius: 20, // Radius에 대한 설정은 해당 부분을 통해서 가능하다. 
        bodySpacing: 5,
        bodyFont: {
          size: 20,
          font: {
            family: `NanumSquareRound`,
          },
        },
        usePointStyle: true,
        callbacks: {
          label: function (context: any) {
            // 기본 설정된 label을 가져올 수 있으며, 여기에 추가적인 텍스트를 기록할 수 있다. 
            // let label = context.dataset.label || '';
            // if (label) {
            //   label = ` ${label} : `;
            // }
            // if (context.parsed.y !== null) {
            //   label += context.parsed.y + '점';
            // }
            const label = ` ${context.parsed.y}점`;
            return label;
          },
          // title을 null로 설정하면, 호버한 x축의 해당 영역을 화면에서 제거할 수 있다. 
          title: function () {
            return null;
          },
        },
      },
  ```

  Y축에 대해서도 따로 언급해보자

  ```javascript
   y: {
          type: 'linear' as const,
          display: true,
          position: 'left' as const,
          // 데이터의 값이 설정한 최소 stepSize 단계보다 높더라도 0점을 표시할 수 있으며
          // 상한선을 제어할 수 있다. 
          beginAtZero: true,
          max: 100,
          ticks: {
            stepSize: 20,
            callback: function (value: number) {
              return value + ' 점';
            },
          },
          gridLines: {
            display: false, // Y축의 세로선 숨김
          },
        },
  ```
</details>
