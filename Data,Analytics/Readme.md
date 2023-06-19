# Athena
- S3 데이터 분석 서비스
- csv, json, avro, parquet 형식 지원
- QuickSight 와 같이 사용된다.
- Serverless SQL Engine
- Columnar Data 사용하여 비용 절감 용이
  - Parquet, ORC 추천 -> 성능 대폭 개선 가능
  - 데이터를 Parquet, ORC 로 migration 하려면 Glue 를 사용할 것 (Glue : 일종의 ETL 서비스)
- For Large Files (> 128MB)
- Federated Query
  - RDS, NoSQL 상관없이 모두 쿼리 가능
  - Data Source Connector 를 Lambda 로 하여, 여러 환경에서 쿼리를 사용할 수 있다. 
  - 그래서 Federated(연합) 쿼리라고 부름

# Redshift
- Postgre 기반 기술 but not used for OLT(Transaction)P
- It's OLAP (Online Analytical Processing) - Athena 도 마찬가지로 분석 서비스긴 함
- 10x better Performance than other `Data warehouses` , scale to PBs of data.
- Athena 와 비슷하게 Columnar Storage of data
- SQL Interface 사용
- QuickSight, Tableau 같은 BI Tool 과 통합 가능
- Athena 와 비교
  - 우선 S3 의 데이터를 모두 Redshift 에 load
  - Index 덕분에 Redshift 의 성능이 더 빠르고, 조인도 쉽다. 
  - 즉 복잡한 쿼리나 조인이 필요한 경우에는 Athena 보다 Redshift 가 적합하다.
- No multi AZ mode
- DR
  - Snapshot 찍어 복제
  - 8시간 주기로 자동 스냅샷도 가능하고, 수동으로 수정하여 찍을 수도 있다.

### Cluster
- 리더 / 컴퓨터 노드로 구성
- 리더 
  - Query Planning, Result Aggregation
- 컴퓨터
  - 실제 쿼리 수행, 결과 리더에게 전송

### 3 way of Loading Data in Redshift : Large Inserts are MUCH Better
1. Kinesis Firehose 로 여러 군데에서 데이터 끌어옴 -> Redshift 에 적재
2. S3 Using Copy command
3. EC2 Instance JDBC Driver