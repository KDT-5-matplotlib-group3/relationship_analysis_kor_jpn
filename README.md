# 희진

### (1) 데이터 확인 및 생성

[Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/c19dc72a-3d43-4061-8eee-c886a3ffbb0f/ba64ac8a-c0c5-4fdf-bc93-6702d8bb1c20/Untitled.xls)

[Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/c19dc72a-3d43-4061-8eee-c886a3ffbb0f/cb0a0868-35fc-4ae3-b139-b60e4efeb74e/Untitled.xlsx)

- xml file → xlsx file로 변환
- 모든 국가 별 수입/수출 데이터 생성
- 한국 전체 수출/수입 데이터 생성
- 데이터 가공 후
    - 한국-일본  수출/수입 데이터 생성
    - 한국-일본 수출 증감률 데이터 생성
    - 2019년~2020년 월 단위 한국-일본 수출/수입 데이터 생성
        - 반일감정이 극대화된 시기와 코로나 시기를 구분해서 데이터를 분석해볼 수 있음을 기대할 수 있음

### (2) 데이터 가공

- 인덱스명이나 컬럼명을 통일
- 모든 국가별  수입/수출 데이터에서 일본 데이터만 추출해서 한국-일본 수출/수입 데이터 생성
    - 멀티인덱스여서 xs(’일본’,level  =1 )을 통해 추출
- 2019-2020년 월 단위 수출 수입량 데이터를 추출하기 위해 인덱스를 datetime으로 바꾸고 year이 2019이거나 year이 2020인 행을 불린 메서드를 이용하여 추출
- 한국 전체 수출/수입 데이터는 년 단위라서 모든 국가 별 수입/수출 데이터로 뽑은 한국-일본 수출 수입 데이터를 월 단위에서 년 단위로 가공
    - 빈 딕셔너리를 제작 후 년도 별 총계 데이터를 계산해서 년도 통합 및 년도 별 총계로 새로운 DF 생성
- 자료들이 서로 단위가 달라서 억만 달러 단위로 통일
    - 한국 전체와 한국-일본 비교는 추세 비교를 위해 한국 전체의 단위를 십억만 달러로 정함
- float type을 가져야하는 데이터가 object type을 가져서 형변환 실행
- 각 추출한 수출/수입 데이터를 concat을 이용해 열 병합

### (3) 그래프 결과

![KorExport.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/c19dc72a-3d43-4061-8eee-c886a3ffbb0f/9def83b5-8a3b-44c7-86fb-273e9bd6f885/KorExport.png)

![KorImport.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/c19dc72a-3d43-4061-8eee-c886a3ffbb0f/316c65b5-fe74-4db5-ae30-07bac96fa416/KorImport.png)

![allKorJap.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/c19dc72a-3d43-4061-8eee-c886a3ffbb0f/216964e6-e339-4bee-9ea9-1410a45990f5/allKorJap.png)

### (4) 인사이트 도출

월 단위 한국-일본 수입/수출량을 봤을 때, 2019년 7월 이후로 부터 유의미하게 수출량이 감소했음을 볼 수 있다. 그에 반해 수입량 그래프는 무난하게 흘러간다. 한국인들의 반일 감정이 수출량에 영향이 있었음을 예측할 수 있다. 그러나 한국 전체 수입/수출량과 비교해서 봤을 때, 전체적으로 감소하는 추세였기 때문에 매우 큰 영향이 있었다고 보기는 힘들다. 

### (5) 피드백

전체적인 수입/수출량에서 일본이 차지하는 비율의 변화가 있었는지도 분석해서 시각화했으면 좋았겠다. 그것이 조금 더 직관적이고 정확한 데이터.

### (6) 내 생각

나라 별 전체적인 수입/수출량 데이터가 존재하기 때문에 거기서 상위 10개 나라를 뽑아서 각 나라별 비율을 도출한 다음에, 이것을 파이차트로 시각화한다면 더욱 정확하고 좋은 자료가 되었을 것 같다. 

이번 팀 프로젝트를 하면서 스스로가 많이 부족하다고 느꼈던 것 같다. 의욕이나 아이디어는 앞서나가고 그걸 뒷받침해줄 역량이나 지식이 없었다. 

### (8) 피드백 후 추가

```python
# 전체 - 일본 량 구하기
KorMinusJanESr = KorAllExport_Sr - KorJapExport_Sr
KorMinusJanESr.dropna(inplace=True)
KorMinusJanISr = KorAllImport_Sr - KorJapImport_Sr
KorMinusJanISr.dropna(inplace=True)

plt.subplot(2,2,1)
plt.pie([KorMinusJanESr['2019년'],KorJapExport_Sr['2019년']],labels=['', '일본 수출량'], autopct="%.2f%%")
plt.legend(loc = 'upper left')
plt.title('2019년 한국 수출량')
plt.subplot(2,2,2)
plt.title('2020년 한국 수출량')
plt.pie([KorMinusJanESr['2020년'],KorJapExport_Sr['2020년']],labels=['', '일본 수출량'], autopct="%.2f%%")
plt.rc('font', size = 7)
plt.subplot(2,2,3)
plt.pie([KorMinusJanISr['2019년'],KorJapImport_Sr['2019년']],labels=['', '일본 수입량'], autopct="%.2f%%")
plt.title('2019년 한국 수입량')
plt.subplot(2,2,4)
plt.title('2020년 한국 수입량')
plt.pie([KorMinusJanISr['2020년'],KorJapImport_Sr['2020년']],labels=['', '일본 수입량'], autopct="%.2f%%")
plt.rc('font', size = 7)
plt.suptitle('[2019-2020년도 일본 수출/입량 파이 분석]')
```

![feedback-grahp.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/c19dc72a-3d43-4061-8eee-c886a3ffbb0f/3ff5f0fb-a71f-45c7-aef1-b658ea569c7f/feedback-grahp.png)

→ 전체 수출/입량에서 일본이 차지하는 비율에 큰 변화가 없다. 전체적인 수출/입량이 줄어든 것이 일본 수출/입량이 줄어든 것에 영향을 준 것이라고 파악된다.

### (9) 결론

<aside>
💡 한국 전체 수입/출량과 비교해서 봤을 때, 전체적으로 감소하는 추세였고 일본이 차지하는 비율에도 큰 변화가 없기 때문에 한국의 반일 감정이 수입/출량에 매우 큰 영향이 있었다고 보기는 힘들다.

</aside>
