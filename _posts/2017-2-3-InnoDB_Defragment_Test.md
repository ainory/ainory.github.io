---
layout: post
title: "mariadb - InnoDB Defragment Test"
categories: "database"
author: "ainory"
---

*   개요
    *   innodb를 사용하다보면 file_per_table 옵션으로 테이블의 파티션 별로 파일을 관리해도 ibdata의 파일 증가는 어쩔수가 없다.
    *   mariadb 10.1.11 버전부터 지원되는 innodb_defragment 옵션을 사용해서 ibdata 파일이 증가하지 않고, 유지가 되는지 확인함.
    *   테스트 버전은 현재 최신 버전인 10.1.21을 사용함.
    *   ibdata 설정은 default로 되어있었기 때문에 10M에 autoextend가 적용되어 있다.
    *   data는 기존에 테스트용으로 사용하고있는 Metatron에서 수집하는 data를 사용한다.
    *   잘만 되면 골칫덩이 ibdata 문제는 더이상 신경안써도 될듯…


*   Issue
    *   ibdata가 여려개일때 적용되지 않는다고 함
    *   ibdata1에 autoextend옵션으로 되어있어야 하는듯.(확인 해봐야함)

  
*   참조
    *   [https://mariadb.org/defragmenting-unused-space-on-innodb-tablespace/](https://mariadb.org/defragmenting-unused-space-on-innodb-tablespace/)
    *   [http://seonguck.blogspot.kr/2014/07/defragmentation-innodb-table-on-mariadb.html](http://seonguck.blogspot.kr/2014/07/defragmentation-innodb-table-on-mariadb.html)
    *   [https://wwarchblog.wordpress.com/2016/04/29/mariadb-optimization-and-tuningtable/](https://wwarchblog.wordpress.com/2016/04/29/mariadb-optimization-and-tuningtable/)

  

*   변수 목록
    *   System variables
        *   Innodb_defragment: InnoDB의 조각모음을 활성화한다.
        *   innodb_defragment_n_pages: 조각모음시 한 타임에 몇 개의 page를 가지고 작업할 것인지를 결정하는 파라미터이다. 기본 값: 7
        *   innodb_defragment_stats_accuracy:  조각모음 통계의 개수… (?)
        *   innodb_defragment_fill_factor_n_recs: 조각모음 수행시 추후 사용을 위해 남겨놓을 페이지 여유분으로 record 건수를 기준으로 함. 기본 값: 20
        *   innodb_defragment_fill_factor: 조각모음 수행시 페이지를 얼마나 채울 지 비율을 결정하는 변수. 기본 값: 0.9
        *   innodb_defragment_frequency: 단일 인덱스에 대해 조각모음을 수행할 수 있는 초당 횟수의 최대값. 기본 값: 40
    *   Status variables
        *   innodb_defragment_compression_failures: 조각모음 재압축의 실패 횟수
        *   innodb_defragment_failures: 조각모음 실패 횟수
        *   innodb_defragment_count: 조각모음 수행 횟수

  

* * *

  

*   시작시 옵션
    *   일단은 default로 테스트(innodb_defragment만 1로 함)
        *   innodb\_defragment\_fill_factor: 0.900000
        *   innodb\_defragment\_fill\_factor\_n_recs: 20
        *   innodb\_defragment\_frequency: 40
        *   innodb\_defragment\_n_pages: 7
        *   innodb\_defragment\_stats_accuracy: 0


*   ibdata size (테스트 시작 이후로 ibdata1 size 변동 없음)

| Date                 | ibdata1 size |
| -------------------- | ------------ |
| 2017년 2월 13일 월요일  | 1419771904   |
| 2017년 2월 14일 화요일  | 1419771904   |
| 2017년 2월 15일 수요일  | 1419771904   |
| 2017년 2월 16일 목요일  | 1419771904   |
| 2017년 2월 17일 금요일  | 1419771904   |
| ...                  | ...          |
| 2017년 2월 27일 월요일  | 1419771904   |