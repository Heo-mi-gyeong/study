## ❗useEffect 배열 요소 변경 인식

 - todolist 수정 시 localStorage에 수정 사항을 반영하려함

 - 일정 목록이 담긴 배열을 useEffect가 인식하지 못하는 문제 

- useEffect 기본 형태 : useEffect ( function , deps)
    - deps(dependence) : 배열 형태, **function을 실행시킬 조건**(특정 값을 넣게되면 컴포넌트가 마운트 될 때, 지정한 값이 업데이트 될 때 function을 실행)

- 기존 코드
    
    ```jsx
    const modify = (targetId, targetContent) => {
            const newItem = object.assign(item);
            newItem.map((data,i) => {
                if(targetId === data.id) {
                    data.content = targetContent;
                }
            });
            setItem(newItem); 
        }
    ```
    

```jsx
	useEffect(() => {
	        if (!isMount.current) {
	            localStorage.setItem('todoList', JSON.stringify(item));
	            localStorage.setItem('id', JSON.stringify(cnt));
	        }
	    }, [item, cnt]);

```
🔴 문제

    - useEffect → item(일정 목록을 담은 배열)의 요소 변경 인식 불가 
    - item과 newItem의 값 비교 시 true 반환 (둘다 변경된 값 반영)

```jsx
	const modify = (targetId, targetContent) => {
	        const newItem = **JSON.parse(JSON.stringify(item));**
	        newItem.map((data,i) => {
	            if(targetId === data.id) {
	                data.content = targetContent;
	            }
	        });
	        setItem(newItem); 
	    }
```

```jsx
	useEffect(() => {
	        if (!isMount.current) {
	            localStorage.setItem('todoList', JSON.stringify(item));
	            localStorage.setI'id', JSON.stringify(cnt));
	        }
	    }, **[JSON.stringify(item)]**);
```

🟢 해결

- 배열 깊은 복사를 이용
    
    **# shallow copy(얕은복사)**
    
    기존 배열의 ‘주소값’을 가져옴
    
    - 단순한 변수 선언을 통한 복사의 형태
    - 복사한 배열을 수정하게 될 경우, 원래 배열 또한 수정되는 결과
    를 얻게 됨
    
    **# deep copy(깊은 복사)**
    
    기존 배열 ‘그대로’ 가져옴
    
    - 원래 배열을 그대로 가져와 새 배열에 덮어쓰기 하는 것
    - 복사한 배열을 수정하더라도, 원래 배열이 수정되는일은 없음
    
    참고 : [https://dev-note-97.tistory.com/m/36](https://dev-note-97.tistory.com/m/36)
    
    **# object.assign(target, …sources) - 기존 코드에 사용**
    
    출처 객체들의 속성을 복사해 대상 객체에 붙여넣어 반환
    
    - ex) `const returnedTarget = Object.assign(target, source);`
        - returmedTarget 에 할당된 객체 = source 객체의 속성이 target 객체에 복사된 객체
    - **target :** 목표 객체. 출처 객체의 속성을 복사해 반영한 후 반환할 객체
    - **sources :** 출처 객체, 목표 객체에 반영하고자 하는 속성들을 갖고 있는 객체

useEffect의 deps에 배열이 아닌 문자열로 변환하여 비교