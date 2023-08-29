---
title: "Slow Query 추적(PostgreSQL)"
layout: post
date: 2023-08-30 08:231
tags: [Blog, slow query, postgreSQL]
star: false
category: blog
author: gyuhwan
toc: true
toc_sticky: true
excerpt: "JPA 환경에서 Slow Query 추적하기 with PostgreSQL"

---

# Slow Query 추적

실제 사내 서버에서 특정 페이지를 조회할 때, 상당한 딜레이 후에 화면이 표출되는 것을 보고 이를 추적했던 과정입니다.  사용 DB는 **PostgreSQL** 입니다.  

### **Intellij 로그 추적**

```yaml
# hibernate statistics
spring.jpa.properties.hibernate.generate_statistics=true
logging.level.org.hibernate.stat=DEBUG
spring.jpa.properties.hibernate.session.events.log.LOG_QUERIES_SLOWER_THAN_MS=10
```

application.properties에 위와 같은 설정을 통하여 **Slow Query**를 추적합니다. 

로그를 통해 두 개의 **SlowQuery 쿼리** 문을 발견했습니다. 

```yaml
11:33:38.053 [http-nio-8400-exec-1] INFO  o.h.e.jdbc.spi.SqlStatementLogger.logSlowQuery 183 - SlowQuery: 19 milliseconds. SQL: 'HikariProxyPreparedStatement@55029049 wrapping select facility0_.facility_seq as facility1_6_, facility0_.administ_zone as administ2_6_, facility0_.alive_check as alive_ch3_6_, facility0_.facility_id as facility4_6_, facility0_.facility_image as facility5_6_, facility0_.facility_instl_dt as facility6_6_, facility0_.facility_instl_info as facility7_6_, facility0_.facility_kind as facility8_6_, facility0_.facility_name as facility9_6_, facility0_.facility_status as facilit10_6_, facility0_.insert_dt as insert_11_6_, facility0_.insert_user_seq as insert_12_6_, facility0_.latitude as latitud13_6_, facility0_.longitude as longitu14_6_, facility0_.station_seq as station15_6_, facility0_.update_dt as update_16_6_, facility0_.update_user_seq as update_17_6_ from t_facility facility0_ where facility0_.facility_kind=75'

11:33:38.275 [http-nio-8400-exec-1] INFO  o.h.e.jdbc.spi.SqlStatementLogger.logSlowQuery 183 - SlowQuery: 185 milliseconds. SQL: 'HikariProxyPreparedStatement@477509515 wrapping select facilityop0_.facility_seq as facility5_8_2_, facilityop0_.facility_opt_seq as facility1_8_2_, facilityop0_.facility_opt_seq as facility1_8_1_, facilityop0_.facility_opt_type as facility3_8_1_, facilityop0_.facility_opt_name as facility2_8_1_, facilityop0_.facility_opt_value as facility4_8_1_, facilityop0_.facility_seq as facility5_8_1_, facilityop0_.insert_dt as insert_d6_8_1_, commoncode1_.code_seq as code_seq1_4_0_, commoncode1_.code_id as code_id2_4_0_, commoncode1_.code_name as code_nam3_4_0_, commoncode1_.code_value as code_val4_4_0_, commoncode1_.insert_dt as insert_d5_4_0_, commoncode1_.insert_user_seq as insert_u6_4_0_, commoncode1_.parent_code_seq as parent_c7_4_0_, commoncode1_.update_dt as update_d8_4_0_, commoncode1_.update_user_seq as update_u9_4_0_, commoncode1_.use_kind as use_kin10_4_0_ from t_facility_opt facilityop0_ left outer join t_common_code commoncode1_ on facilityop0_.facility_opt_type=commoncode1_.code_seq where facilityop0_.facility_seq in (9967, 9892, 9893, 9894, 9895, 9896, 9897, 9898, 9863, 9864, 9865, 9866, 9867, 9868, 9905, 9906, 9907, 9908, 9909, 9910, 9911, 9912, 9913, 9914, 9915, 9916, 9917, 9918, 9919, 9920, 9921, 9922, 9923, 9924, 9725, 9726, 9727, 9728, 9729, 9796, 9797, 9798, 9925, 9926, 9927, 9928, 9929, 9935, 9936, 9937, 9938, 9939, 9940, 9941, 9942, 9943, 9931, 9932, 9933, 9934, 9730, 9731, 9732, 9733, 9799, 9800, 9801, 9944, 9945, 9946, 9947, 9948, 9949, 9950, 9951, 9952, 9953, 9734, 9735, 9736, 9737, 9738, 9739, 9740, 9741, 9742, 9743, 9744, 9745, 9802, 9803, 9804, 6907, 9746, 9747, 9748, 9749, 9750, 9751, 9752, 9753, 6659, 9754, 9755, 9756, 9757, 9758, 9716, 9930, 9759, 9760, 9761, 6660, 9715, 9719, 9720, 9762, 9763, 9764, 9765, 9766, 6661, 9723, 9724, 9767, 9768, 9769, 9770, 9771, 9772, 9773, 9774, 9775, 9776, 9777, 9778, 9779, 9780, 9781, 9782, 9783, 9784, 9785, 9786, 9787, 9194, 9224, 9968, 9969, 9966, 9970, 9971, 9312, 9313, 9314, 9315, 9316, 9317, 9318, 9319, 9320, 9321, 9322, 9323, 9324, 9325, 9326, 9327, 9328, 9329, 9330, 9331, 9332, 9333, 9334, 9335, 9337, 9338, 9339, 9340, 9341, 9342, 9343, 9344, 9345, 9346, 9347, 9348, 9349, 9350, 9351, 9352, 9353, 9354, 9355, 9356, 9357, 9358, 9359, 9360, 9361, 9362, 9363, 9364, 9365, 9366, 9367, 9368, 9369, 9370, 9371, 9372, 9373, 9374, 9375, 9376, 9377, 9378, 9379, 9380, 9381, 9382, 9383, 9384, 9385, 9386, 9387, 9388, 9389, 9390, 9391, 9392, 9393, 9394, 9395, 9396, 9397, 9398, 9399, 9400, 9401, 9402, 9403, 9404, 9405, 9406, 9407, 9408, 9409, 9410, 9411, 9412, 9413, 9414, 9415, 9416, 9417, 9418, 9419, 9420, 9421, 9422, 9423, 9424, 9425, 9426, 9427, 9428, 9429, 9430, 9431, 9432, 9433, 9434, 9435, 9436, 9437, 9438, 9439, 9440, 9441, 9442, 9443, 9444, 9445, 9446, 9447, 9448, 9449, 9450, 9451, 9452, 9453, 9454, 9455, 9456, 9457, 9458, 9459, 9460, 9461, 9462, 9463, 9464, 9465, 9466, 9467, 9468, 9469, 9470, 9471, 9472, 9473, 9474, 9475, 9476, 9477, 9478, 9479, 9480, 9481, 9482, 9483, 9484, 9485, 9486, 9487, 9488, 9489, 9490, 9491, 9492, 9493, 9494, 9495, 9496, 9497, 9498, 9499, 9500, 9501, 9502, 9503, 9504, 9505, 9506, 9507, 9508, 9509, 9510, 9511, 9512, 9513, 9514, 9515, 9516, 9517, 9518, 9519, 9520, 9521, 9522, 9523, 9524, 9525, 9526, 9527, 9528, 9529, 9530, 9531, 9536, 9537, 9538, 9539, 9540, 9541, 9542, 9543, 9544, 9545, 9546, 9547, 9548, 9549, 9550, 9551, 9552, 9553, 9554, 9555, 9556, 9557, 9558, 9559, 9560, 9561, 9562, 9563, 9564, 9565, 9566, 9567, 9568, 9806, 9532, 9814, 9815, 9816, 9533, 9535, 9534, 6658, 9807, 9817, 9818, 9819, 9808, 9820, 9821, 9822, 9713, 9714, 9809, 9810, 9826, 9827, 9828, 8670, 8679, 8688, 8897, 8898, 9192, 6802, 9193, 9700, 6765, 9699, 9701, 9702, 9703, 9704, 9705, 9706, 9707, 9708, 9709, 9710, 9711, 9712, 9717, 9718, 9823, 9824, 9825, 9830, 9831, 9832, 9336, 9903, 9904, 9793, 9794, 9795, 9899, 9900, 9901, 9902, 9829, 9833, 9834, 9835, 9862, 9869, 9870, 9871, 9872, 9873, 9221, 9229, 9242, 9874, 9875, 9876, 9877, 9878, 9879, 9880, 9881, 9882, 9883, 9884, 9885, 9886, 9887, 9888, 9889, 9890, 9891)'
```

로그에 찍힌 SQL을 Intellij 를 통해 추적하기 앞서 먼저 sql 내부에서 어떤 식으로 쿼리가 진행되는지 확인했습니다. 

### PostgreSQL Explain

쿼리의 성능을 분석하기 위해 ‘**Explain**’ or ‘**Explain analyze**’ 를 사용하면 실제 SQL 에서 실행되는 실행 계획을 확인할 수 있습니다.  sql query 문을 작성해봅니다. 

```yaml
explain analyze select * ~~~~~~ '위의 쿼리'
```

**결과**

쿼리문을 날리면 아래와 같이 **Query Plan**을 확인이 가능합니다. 

![slow-query1](/assets/images/experionce/slow_query1.png)

pgAdmin 을 사용시 ‘**F7**’ or ‘**F7 + Shift**’ 를 사용하면 조금 더 자세한 정보를 출력해줍니다. 

![slow-query2](/assets/images/experionce/slow_query2.png)

- **Explain analyze 분석하기**
    
  ![slow-query3](/assets/images/experionce/slow_query3.svg)
    
    Explain 을 통해 나온 자료를 통해 분석해봅시다. 실제 쿼리문을 실행되지 않고 실행했을 때 예측되는 정보들입니다. 
    
    ![slow-query5](/assets/images/experionce/slow_query5.png)
    
    - **Hash Left Join** : 데이터를 생성 및 처리 방식
    - **cost=6.29..54294.91** : 처리하는데 필요한 COST인데. 처음 6.29는 시작 비용입니다. **Seq Scan**의 경우, 바로 시작할 수 있기 때문에 0으로 측정됩니다. ‘..54294.91’  값은 전체 비용입니다.
    - **rows** : 예상 rows 수 입니다.  뒤에 actual time을 보면 ‘rows=3473’ 값으로 비슷한 값을 추정한 것을 알 수 있는데. PostgreSQL 은 테이블에 대한 통계를 보관하고 있기 때문에 이렇게 쿼리 실행 후 예상 값을 추측이 가능한 겁니다.
    - **width** : row당 평균 byte 수
    
    **실행 순서**
    
    ![slow-query4](/assets/images/experionce/slow_query4.png)
    
    1) t_common_code 를 **Seq Scan** 으로 전체를 조회합니다. 
    
    2) 거의 동시에 **Hash, Seq Scan**을 하여 조회
    
    3) 나왔던 데이터를 **Hash Left Join** 을 통해 최종적으로 조회를 하게 됩니다. 
    
    실행 순서의 경우 옵티마이저가 선택하기 때문에 Explain analyze는 참고만 하는 것이 좋습니다. 
    
    문서를 살펴보면 좀 더 자세한 분석이 가능하지만 그 내용은 본문의 주제와 벗어나기 때문에 넘어가겠습니다. 잘보면 Seq Scan을 두 번 반복된다는 것을 알 수 있습니다. 
    
    Seq Scan는 모든 데이터를 조회하기 때문에 성능면에서 좋지 않을 수 있습니다. 이럴 때는 전략적으로 **index** 를 설정하여 **index scan**을 유도하는 것이 좋습니다. 
    
    하지만 인덱스를 무작정 생성하는 것은 오히려 성능을 악화시키기 때문에 잘 알고 사용하는 것이 좋습니다. 
    
    인덱스 관련 정보는 아래의 링크를 통해 자세히 설명되어 있습니다.
    
    [[Database\] 인덱스(index)란?](https://mangkyu.tistory.com/96)