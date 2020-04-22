---
layout: single
title:  Strassen Algorithm
date:   2020-04-08 17:33:09 +0900
categories: jekyll update
author : Sang Jin
---
### 슈트라센 알고리즘

---



행렬의 곱연산을 수행함에 있어 행렬이 커지면 커질수록 연산에 속도는 기하급수적으로 증가할 수밖에 없는 구조이다. 행렬의 곱은 각 원소를 곱한 후에 나온 결과를 더해 최종 행렬이 생성이 되며 행렬의 크기가 커질수록 곱하기 연산은 증가 할 수밖에 없다. cpu에 구조상 더하기 연산이 빠르기 때문에 스트라센 알고리즘에서는 곱하기 연산을 더하기 연산으로 치환하여 계산하도록 알고리즘을 보안했다.



### 알고리즘 방법

---



행렬 A, B와 두 행의 곱의 결과 C가 있다고하자.

![](https://t1.daumcdn.net/cfile/tistory/255BA03A5818B52830) 

일반적인 행렬의 곱은 다음과 같으며, 총 8번의 곱셉과 네번의 덧셈으로 연산된다.

![](https://t1.daumcdn.net/cfile/tistory/216A1B365818B6470D) 

스트라센에서 행렬의 곱셉을 더하기 연산으로 풀어 각 원소를 구할 수 있는 M이라는 연산 행렬로 표현한다.

이러한 M행렬은 일곱번의 곱셈과 10번의 덧셈으로 연산으로 나타낼 수 있으며 아래 와 같이 표현한다.

![](https://t1.daumcdn.net/cfile/tistory/2735784D53F4468D1E) 

![](https://t1.daumcdn.net/cfile/tistory/243C814D53F4468E12) 

![](https://t1.daumcdn.net/cfile/tistory/2333624D53F4468E23) 

![](https://t1.daumcdn.net/cfile/tistory/2434124D53F4468E21) 

![](https://t1.daumcdn.net/cfile/tistory/2727B24D53F4468E35) 

![](https://t1.daumcdn.net/cfile/tistory/213F594D53F4468E0D) 

![img](https://t1.daumcdn.net/cfile/tistory/21458A4D53F4468E04) 

최종 C행렬은 M행렬의 더하기 연산으로 이루어져 있으며, 각 원소에 해당하는 방법이있다.

![](https://t1.daumcdn.net/cfile/tistory/264E3D3753F446C624) 

![](https://t1.daumcdn.net/cfile/tistory/213BE73753F446C634) 

![](https://t1.daumcdn.net/cfile/tistory/2526A83753F446C642) 

![](https://t1.daumcdn.net/cfile/tistory/25654A3753F446C715) 



### 결론

---



스트라센의 경우 행렬의 곱셉을 하기 위해서는 정사각행렬에 대해서만 처리가 가능하다. 그렇지 않을 경우에는 행렬을 정사각 행렬로 변경하는 작업이 필요하다. 또한, 특정 단계에서는 행렬의 곱이 더 빠른 구간이 있으며 스트라센 행렬에서는 최적의 행렬 크기에서는 일반곱으로 행렬을 풀어나가는 방법이 있다. 스트라센 알고리즘에서 또 눈여겨 볼 부분은 연산으로 사용하는 M행렬을 구하는 부분에서도 행렬의 곱이 쓰인다는 점이다. 행렬의 곱은 스트라센으로 풀어나가는 알고리즘이기 때문에 M1을 예로 들면 M1 := (A + A) strassen (A + B) 이런식으로 풀어 쓸수 있다. 결국에는 재귀적인 호출을 통해 스트라센을 구해 나가는 방식을 이용하는 알고리즘인것 이다. 분할 정복알고리즘과 동일하며, M에서는 각 행렬을 작은 단위로 분할하며 최종 C행렬을 구하기 위해서는 분할된 원소를 재조립하는 과정으로 최종 행렬을 얻어낼 수 있다.



### 소스코드

---

~~~java
 public class Strassen {
       public static void main(String[] args) {
             int n = 1024;
             int[][] x = initMetrix(n);
             int[][] y = initMetrix(n);     
             int[][] nomalResult = Strassen.metrixMul(n, x, y);
             Strassen strassen = new Strassen();
             int[][] strassenReslut = strassen.excuteStrassen(x, y);  
             boolean checkMetrix = true;
             for (int i = 0; i < n; i++) {
                    for (int j = 0; j < n; j++) { 
                           if (nomalResult[i][j] != strassenReslut[i][j]) {
                                 checkMetrix = false;
                           }
                    }
             }
             System.out.println("결과 : " + checkMetrix);       
       }

       public static int[][] initMetrix(int n) {
             Random r = new Random();
             int[][] resultMetrix = new int [n][n];
             for (int i = 0; i < n; i++) {
                    for (int j = 0; j < n; j++) {
                           resultMetrix[i][j] = r.nextInt(30);
                    }
             }
             return resultMetrix;
       }
     
       public int[][] excuteStrassen(int[][] metrixX, int[][] metrixY) {
 
             // 스트라센의 경우 n*n 행렬로 연산
             int n = metrixX.length;

             // 임계 차원 보다 작을 경우 기존 메트릭스 곱으로 풀이
             if (n <= 2) {

                    return metrixMul(n, metrixX, metrixY);
             }
 
             // 4 등분
             int rank = n / 2;
           
             // 배열 분해
             int[][] a11 = subMetrix(rank, 0, 0, metrixX);

             int[][] a12 = subMetrix(rank, 0, rank, metrixX);

             int[][] a21 = subMetrix(rank, rank, 0, metrixX);

             int[][] a22 = subMetrix(rank, rank, rank, metrixX);

             int[][] b11 = subMetrix(rank, 0, 0, metrixY);

             int[][] b12 = subMetrix(rank, 0, rank, metrixY);

             int[][] b21 = subMetrix(rank, rank, 0, metrixY);

             int[][] b22 = subMetrix(rank, rank, rank, metrixY);
              
             int[][] m1 = excuteStrassen(metrixSum(a11, a22), metrixSum(b11, b22)); // m1=(a11+a11)(b11+b22)

             int[][] m2 = excuteStrassen(metrixSum(a21, a22), b11); // m2=(a21+a22)b11

             int[][] m3 = excuteStrassen(a11, metrixSub(b12, b22)); // m3=a11(b12-b22)

             int[][] m4 = excuteStrassen(a22, metrixSub(b21, b11)); // m4=a22(b21-b11)

             int[][] m5 = excuteStrassen(metrixSum(a11, a12), b22); // m5=(a11+a12)b22

             int[][] m6 = excuteStrassen(metrixSub(a21, a11), metrixSum(b11, b12)); // m6=(a21-a11)(b11+b12)

             int[][] m7 = excuteStrassen(metrixSub(a12, a22), metrixSum(b21, b22)); // m7=(a12-a22)(a21+b22)

             // 결과 생성
             int[][] c11 = metrixSum(metrixSub(metrixSum(m1, m4), m5), m7); // c11 = m1 + m4 - m5 + m7
             int[][] c12 = metrixSum(m3, m5); // c12 = m3 + m5
             int[][] c21 = metrixSum(m2, m4); // c21 = m2 + m4
             int[][] c22 = metrixSum(metrixSub(metrixSum(m1, m3), m2), m6); // c22 = m1 + m3 - m2 + m6        
             // 결합
             return combin(c11, c12, c21, c22);
       }
       private int[][] combin(int[][] c11, int[][] c12, int[][] c21, int[][] c22) {
             int n = c11.length;
             int[][] resultMetrix = new int [n*2][n*2];
             for (int i = 0; i < n; i ++) {
                    for (int j = 0; j < n; j++) {
                           resultMetrix[i][j] = c11[i][j]; // 11
                           resultMetrix[i][j + n] = c12[i][j]; // 12
                           resultMetrix[i + n][j] = c21[i][j]; // 21
                           resultMetrix[i + n][j + n] = c22[i][j]; // 22
                    }
             }
             return        resultMetrix;
       }

       private int[][] subMetrix(int n, int startX, int startY, int[][] metrix) {           
             int[][] subMetirx = new int[n][n];
             for (int i = 0, x = startX; i < n; i++, x++) {
                    for (int j = 0, y = startY; j < n; j++, y++) {
                           subMetirx[i][j] = metrix[x][y];
                    }
             }
             return subMetirx;
       }     
       private int[][] metrixSum(int[][] metrixX, int[][] metrixY) {
             int n = metrixX.length;
             int[][] metrixResult = new int[n][n];
             for (int i = 0; i < n; i++) {
                    for (int j = 0; j < n; j++) {
                           metrixResult[i][j] = metrixX[i][j] + metrixY[i][j];
                    }
             }
             return metrixResult;
       }  
       private int[][] metrixSub(int[][] metrixX, int[][] metrixY) {
             int n = metrixX.length;
             int[][] metrixResult = new int[n][n];
             for (int i = 0; i < n; i++) {
                    for (int j = 0; j < n; j++) {
                           metrixResult[i][j] = metrixX[i][j] - metrixY[i][j];
                    }
             }
             return metrixResult;
       }

       public static int[][] metrixMul(int n, int[][] metrixX, int[][] metrixY) {        
             int [][] result = new int[n][n];
             for (int i = 0; i < n; i++) {
                    for (int j = 0; j < n; j++) {
                           for (int k = 0; k < n; k++) {
                                 result[i][j] += metrixX[i][k] * metrixY[k][j];
                           }
                    }
             }
             return result;
       }
}
~~~