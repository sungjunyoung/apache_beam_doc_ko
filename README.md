# apache_beam_doc_ko
> 아파치 Beam 도큐멘테이션 번역  
>
> 역자주  
> [Apache Beam Programming Guide](https://beam.apache.org/documentation/programming-guide/#overview)를 번역한 글입니다. 본문은 one page 구성이나, 편의상 폴더별로 나누어 작성했습니다.

## Contents

- [Overview](https://github.com/sungjunyoung/apache_beam_doc_ko/tree/master/1.%20Apache%20Beam%20Programming%20Guide/1.%20Overview)
- [Creating the Pipeline](https://github.com/sungjunyoung/apache_beam_doc_ko/tree/master/1.%20Apache%20Beam%20Programming%20Guide/2.%20Creating%20the%20pipeline)
- [Working with PCollections](https://github.com/sungjunyoung/apache_beam_doc_ko/tree/master/1.%20Apache%20Beam%20Programming%20Guide/3.%20Working%20with%20PCollections)
    - [Creating a PCollection](https://github.com/sungjunyoung/apache_beam_doc_ko/tree/master/1.%20Apache%20Beam%20Programming%20Guide/3.%20Working%20with%20PCollections/1.%20Creating%20a%20PCollection)
    - [PCollection Characteristics](https://github.com/sungjunyoung/apache_beam_doc_ko/tree/master/1.%20Apache%20Beam%20Programming%20Guide/3.%20Working%20with%20PCollections/2.%20PCollection%20characteristics)
        - Element Type
        - Immutability
        - Random Access
        - Size and Boundedness
        - Element Timestamps
- [Applying Transforms](https://github.com/sungjunyoung/apache_beam_doc_ko/tree/master/1.%20Apache%20Beam%20Programming%20Guide/4.%20Applying%20transforms)
    - [Using ParDo](https://github.com/sungjunyoung/apache_beam_doc_ko/tree/master/1.%20Apache%20Beam%20Programming%20Guide/4.%20Applying%20transforms/1.%20Using%20ParDo)
    - [Using GroupByKey](https://github.com/sungjunyoung/apache_beam_doc_ko/tree/master/1.%20Apache%20Beam%20Programming%20Guide/4.%20Applying%20transforms/2.%20Using%20GroupByKey)
    - [Using Combine](https://github.com/sungjunyoung/apache_beam_doc_ko/tree/master/1.%20Apache%20Beam%20Programming%20Guide/4.%20Applying%20transforms/3.%20Using%20Combine)
    - Using Flatten and Partition
    - General Requirements for Writing User Code for Beam Transforms
    - Side Inputs and Side Outputs
- Composite Transforms
- Pipeline I/O
- Running the Pipeline
- Data Encoding and Type Safety
- Working with Windowing
- Working with Triggers
