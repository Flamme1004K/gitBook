---
description: 'https://programmers.co.kr/learn/courses/30/lessons/42626'
---

# 더맵게

![](../.gitbook/assets/image%20%2817%29.png)



 힙 정렬에 대한 문제는 요번이 처음이였다. 

 그럼 힙 정렬에 대한 문제는 어떻게 풀어야 할까? 부터 생각부터 하였다

 몇가지의 생각을 했는데..

1. 힙이란 과연 무엇일까?
2. 코테에서 힙문제는 어떻게 풀어야 할까?
3. 힙 문제를 보니 1,000,000 그리고 1,000,000,000 이란 범위가 있으니 시간복잡도와 연관이 되어있겠네?

 이 세가지 였다.

##   힙이란 과연 무엇일까?

 짧게 말해서 힙은 

 **여러 개의 값들 중에서 최댓값이나 최솟값을 빠르게 찾아내도록 만들어진 자료구조** 

 라고 한다.

##   코테에서 힙문제는 어떻게 풀어야 할까?

 코테에서 힙문제를 어떻게 풀어야할까 라는 고민을 하는 도중에 가장 기본적인 힙문제는 어떻게 풀어지는지 봤다. 우리 구글신에게 물어본 결과 거의다 배열에 값을 넣고 대갓 최솟값에 대해 sort를 하여 풀어지는 것을 봤다.

 생각을 하던 차에 몇가지 생각을 하였다.

1. 일단 배열에 다 넣자.
2. 자바 sort 함수를 하자.
3. 이건 배열이 다시 만들어지니깐 재귀호출을 하자.

 라는 생각으로 일단 문제를 풀었다.

 결과는 이렇다.

```text
import java.util.Arrays;

class Solution { 

    public int solution(int[] scoville, int K) { 
        int count = 0; 
        return returnMix(scoville, K, count); 
    } 
    
    private int returnMix( int[] numbers, int K, int count ) { 
    
        int[] answer = new int[numbers.length-1]; 
        
        Arrays.sort(numbers);
    
        if(numbers[0] < K) {

            answer[0] = numbers[0] + (numbers[0+1]*2);
                
            for(int i = 1; i<answer.length; i++) {
                
                answer[i] = numbers[i+1];
            }
            
            count++;
            
            return returnMix(answer, K, count);
    
        } else {
            return count;
        }
}
```

 

![](../.gitbook/assets/image%20%2818%29.png)

 무수한 런타임 에러와... 호율성 테스트 결과 0... 너무 처참했다...

 이 결과를 받고.. 음... 그러면.. 일단 효율성 테스트 부터 어떻게 만들어 내자 라는 생각을 가지게 되었다.

 시간 초과... 음.. 생각하다보니 범위가 

* scoville의 길이는 2 이상 1,000,000 이하입니다.
* K는 0 이상 1,000,000,000 이하입니다.
* scoville의 원소는 각각 0 이상 1,000,000 이하입니다.

 이정도였다.

 그러면 for문과 재귀호출을 하면.. 시간복잡도가... 

##   힙 문제를 보니 1,000,000 그리고 1,000,000,000이란 범위가 있으니 시간 복잡도와 연관이 되어있겠네?

