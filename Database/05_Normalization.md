# Normalization
* conceptual design (E-R Modeling) 을 올바르게 하지 못한 경우
* relation schema로 변환 시 좋지 않은 relation이 발생할 수 있음
* 더 효율적인 relation으로 구성되도록 정제하는 과정

### "bad" form?
* 중복되는 정보의 반복
* 정보를 수정 시 inconsistency 발생 가능
* 예를 들어 교수와 학과 relation을 합치는 경우
    * 같은 학과의 교수가 여러명이면
    * 학과명, 건물 등 정보가 중복되어 나타남

> **IDEA** : 좋은 형태가 될 때까지 관계를 분할

<br/>

## Functional Dependencies
* 키의 일반화 개념. 특정 속성들의 값이 다른 속성들의 값을 unique하게 결정함
* legal relation $r(R)$에서 임의의 두 튜플 $t_1, t_2$에 대해
* $t_1[\alpha]=t_2[\alpha] \Rightarrow t_1[\beta]=t_2[\beta]$ 일 경우 $\alpha \rightarrow \beta$의 functional dependency 성립
* superkey : $K \rightarrow R$, candidate key (minimal)
* $\beta \subseteq \alpha$인 경우 trivial


<br/>

## Lossy vs Lossless-join Decomposition
### Lossy Decomposition
* 관계를 분할 후 다시 natural join 시 원래의 관계와 같아야 함
* join 시 원래의 정보가 손실되는 경우 Lossy

### Lossless-join Decomposition
* r을 $\Pi_{R_1}(r)$, $\Pi_{R_2}(r)$ 두 관계로 분할
* $r=\Pi_{R_1}(r) \Join 
\Pi_{R_2}(r)$ 인 경우
* 다음 두 functional dependency 중 하나가 $F^+$에 포함되어야 함
    * $R_1 \cap R_2 \rightarrow R_1$
    * $R_1 \cap R_2 \rightarrow R_2$

<br/>

## Normal Form
* $5NF \subset 4NF \subset BCNF \subset 3NF \subset 2NF \subset 1NF$

### First Normal Form
* `atomic` domain : 도메인의 값들이 나눌 수 없는 경우
* `1NF` : 모든 도메인이 atomic한 경우
* relational model의 경우 속성이 multivalued/composite 값을 가지지 않음
* 그러므로 모든 relation은 1NF에 속함

### Boyce-Codd Normal Form (BCNF)
* F의 closure F+의 모든 $\alpha \rightarrow \beta$에 대해
    * $\alpha \rightarrow \beta$ is trivial
    * $\alpha$ is superkey for R
    * 둘 중 하나 이상이 성립한다면 BCNF

### BCNF Decomposition
* 관계가 BCNF 만족하지 않을 경우 BCNF를 만족하도록 분할
* non trivial $\alpha \rightarrow \beta$ 에 대해
    * $\alpha \cup \beta$ 와
    * $R - (\beta - \alpha)$ 로 분할
    * 분할이 lossless인지 검사
    * $R - (\beta - \alpha)$가 BCNF인지 검사 후 더 분할할지 결정 

<br/>

## Closure
* F의 closure : F의 모든 functional dependency로부터 논리적으로 추론할 수 있는 functional dependency의 집합
* $F^{+}$ 로 표기

### Armstrong's Axiom을 통한 F+ 계산
* reflexivity : $\beta \subseteq \alpha$ 라면 $\alpha \rightarrow \beta$
* augmentation : $\alpha \rightarrow \beta$ 라면 $\gamma\alpha \rightarrow \gamma\beta$
* transitivity : $\alpha \rightarrow \beta, \beta \rightarrow \gamma$ 라면 $\alpha \rightarrow \gamma$

```python
F+ = F
repeat
    for f in F+:
        F+.add(reflexivity(f))
        F+.add(augmentation(f))
    for f1, f2 in F+:
        if f1, f2 combinable:
            F+.add(transitivity(f1, f2))

until F+ dont change
```

### Closure of Attribute Sets 통한 F+ 계산
* F에 대해, attribute의 집합 $\alpha$의 closure의 정의
    * F에 대해 알파로 결정할 수 있는 attr의 집합
    * $\alpha^{+}$ 로 표기
```python
result = alpha
while result changes:
    for b -> c in F:
        if b in result:
            result.add(c)
```

#### 계산 과정
1. attr의 멱집합의 공집합을 제외한 모든 원소 $\alpha$에 대해 $\alpha^{+}$ 계산
2. 계산된 $\alpha^{+}$를 분해해 F+에 추가
    * 예를 들어, {B}의 clousre가 {B, C}인 경우
    * B->B, B->C, B->BC를 F+에 추가