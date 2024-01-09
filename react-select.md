## react-select

리액트 라이브러리 가운데 `react-select`은 select에 대한 코드작성의 유연과 부수적인 기능을 쉽게 구현하도록 제공한다. 
해당 라이브러리를 사용할 것인지에 대한 여부는 팀에서 정하기 나름이지만, 현재 속한 팀에서 적극적으로 사용 중이기에 학습하게 되었다. 

### 1. react-select 를 사용하는 이유
#### 첫째, 커스터마이징 및 스타일링'의 유연함
- 먼저 '커스터마이징 및 스타일링'의 유연함 : 해당 라이브러리는 커스텀 스타일을 쉽게 적용할 수 있는데, 사용자가 디자인 요구에 맞게 셀렉트 박스를 스타일링할 수 있다.

[공식문서](https://react-select.com/styles)에 설명이 있지만, 자세한 설명이 되어 있지 않다는 부분이 아쉽다. 

- clearIndicator
- container
- control
- dropdownIndicator
- group
- groupHeading
- indicatorsContainer
- indicatorSeparator
- input
- loadingIndicator
- loadingMessage
- menu
- menuList
- menuPortal
- multiValue
- multiValueLabel
- multiValueRemove
- noOptionsMessage
- option
- placeholder
- singleValue
- valueContainer

스타일의 대상은 `import Select from 'react-select'`에 내장된 Select 컴포넌트 안에 있는 요소들에 대한 설정이며, 각 학목들이 위에 나열한 컴포넌트들이다. 아래의 코드는 실제로 UI에 적용하기 위해 설정한 소스코드의 일부이다. 

```javascript
import React from 'react';
import Select from 'react-select';

const customStyles = {
    /*
    * 첫째, Select 박스 : 전체를 감싸고 있는 컴포넌트이며, 최상단에 위치한 컴포넌트이다.
    * focus 되었을 때 보여지는 파란색 아웃라인은 `boxShadow`을 통해서 제어되며, 배경색으로부터 시작해서 전체적인 부분의 스타일 조정이 가능하다.
    * 이때, ':hover': 를 통해 호버시의 스타일을 추가 했는데, 호버에 따라서 색을 제어하는 것도 가능하다. 
    */
    control: (provided) => ({
      ...provided,
      marginTop: 10,
      boxShadow: 'none',
      backgroundColor: '#191919',
      border: '1px solid white',
      borderRadius: '10px',
      ':hover': {
        border: '1px solid white',
      },
    }),
    /*
    * Select에 의해서 선택된 요소가 표시되는 공간이며, 해당 요소의 색상의 변경은 `singleValue` 단계에서 수행되어야 한다.
    * 공식문서에서 이에 대한 깊이를 명시적으로 표현해주면 좋은데, 이 부분이 아쉽다. 가장 찾기에 난해한 부분이었다.
    * 프로젝트에서 배경색을 검은색으로 설정했기에, 글자의 색상 변경은 `singleValue` 단계를 통해 제어할 수 있었다. 
    */
    singleValue: (provided) => ({
      ...provided,
      color: 'white',
    }),
    /*
    * singleValue와 dropdownIndicator 사이에 있는 공간에 있는 세로선에 대한 설정은 `indicatorSeparator`를 통해 제어할 수 있다.
    * 프로젝트에서는 이를 보여주지 않도록 디자인 했는데, `display: 'none'`를 처리함으로 접근했다.
    * 문제는 역시 공식문서에서 해당 컴포넌트의 이름을 명시적으로 보여주지 않는다는 것이다. 
    */
    indicatorSeparator: (provided) => ({
      ...provided,
      display: 'none',
    }),
    /*
    * dropdownIndicator에 대한 설정은 여기를 통해서 가능하다. 
    */
    dropdownIndicator: (provided) => ({
      ...provided,
      color: '#555',
      ':hover': {
        color: 'white',
      },
    }),

    /*
    * menu 는 dropdownIndicator를 통해서 활성화된, options의 테두리 컴포넌트이다.  문제는 menu와 실제 안의 내부 요소들인 option 사이 위 아래로 여백이 발생된다는 점이었다.
    * 이는 menu 와 option 사이에 있는 menuList 컴포넌트를 제어함으로 가능한데, 이 부분 역시 찾기에 어려웠다.
    * 전체적인 스타일이 흰색일 때에는 보이지 않았던 부분이지만, 검은색으로 작업하는 부분에 있어서 해당 부분에 대한 처리가 필요했다.
    * options은 개별 요소들에 대한 설정을 하게 하는데 이때, 내부적으로 state.isSelected를 추적하여, 조건에 따른 스타일 처리가 간편하도록 설정되어 있었다. 
    */
    menu: (provided) => ({
      ...provided,
      borderRadius: 10,
      border: '1px solid white',
      overflow: 'hidden',
    }),
    menuList: (provided) => ({
      ...provided,
      padding: '0px',
    }),
    option: (provided, state) => ({
      ...provided,
      color: state.isSelected ? 'black' : 'white',
      backgroundColor: state.isSelected ? 'white' : '#191919',
      ':hover': {
        backgroundColor: '#ccc',
      },
    }),
    
  };
  const options = student?.ucode && [
    { value: student?.ucode, label: student?.ucode },
    { value: `${student?.ucode}+1`, label: `${student?.ucode}+1` },
    { value: `${student?.ucode}+2`, label: `${student?.ucode}+2` },
  ];

  const handleChange = (selectedOption) => {
    console.log('Selected Option:', selectedOption);
  };

 <Select
        options={options}
        defaultValue={{ value: '23-10101', label: '23-10101' }}
        styles={customStyles}
        onChange={handleChange}
      />
```

#### 둘째, 검색기능을 제공
react-select는 검색기능을 간편하게 제공하는데, 내부로직으로 Select의 options의 label을 탐색하고 관련된 요소들만 추려서 펼쳐진 menu > menuList 안에 배치시킨다. 

#### 셋째, 애니메이션 효과
이러한 효과들을 동작시키는 부분에 있어서 별도의 처리 없이 애니메이션 효과를 제공한다. 

#### 넷째, 상태 관리 및 이벤트 핸들링
선택된 요소의 값은 `onChange={handleChange}` 함수를 통해서 차리가 가능한데, 이를 통해서 비동기 처리를 후속적으로 요청할 수 있을 것이다. 현재의 코드는 우선 변경시마다 선택된 요소의 값이 콘솔에 출력되도록 설정해 두었다. 

