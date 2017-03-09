# apache_beam_doc_ko
> 아파치 Beam 도큐멘테이션 번역  
>
> 역자주  
> [Apache beam](https://beam.apache.org/) documentations 를 번역한 글입니다. 공부 목적이나 마음껏 사용하세요 :)

## Contents

- **[Apache Beam Programming Guide](https://github.com/sungjunyoung/apache_beam_doc_ko/tree/master/1.%20Apache%20Beam%20Programming%20Guide)**
    - [Overview](https://github.com/sungjunyoung/apache_beam_doc_ko/tree/master/1.%20Apache%20Beam%20Programming%20Guide/1.%20Overview)
    - [Creating the Pipeline](https://github.com/sungjunyoung/apache_beam_doc_ko/tree/master/1.%20Apache%20Beam%20Programming%20Guide/2.%20Creating%20the%20pipeline)
    - [Working with PCollections](https://github.com/sungjunyoung/apache_beam_doc_ko/tree/master/1.%20Apache%20Beam%20Programming%20Guide/3.%20Working%20with%20PCollections)
        - [Creating a PCollection](https://github.com/sungjunyoung/apache_beam_doc_ko/tree/master/1.%20Apache%20Beam%20Programming%20Guide/3.%20Working%20with%20PCollections/1.%20Creating%20a%20PCollection)
        - [PCollection Characteristics](https://github.com/sungjunyoung/apache_beam_doc_ko/tree/master/1.%20Apache%20Beam%20Programming%20Guide/3.%20Working%20with%20PCollections/2.%20PCollection%20characteristics)
    - [Applying Transforms](https://github.com/sungjunyoung/apache_beam_doc_ko/tree/master/1.%20Apache%20Beam%20Programming%20Guide/4.%20Applying%20transforms)
        - [Using ParDo](https://github.com/sungjunyoung/apache_beam_doc_ko/tree/master/1.%20Apache%20Beam%20Programming%20Guide/4.%20Applying%20transforms/1.%20Using%20ParDo)
        - [Using GroupByKey](https://github.com/sungjunyoung/apache_beam_doc_ko/tree/master/1.%20Apache%20Beam%20Programming%20Guide/4.%20Applying%20transforms/2.%20Using%20GroupByKey)
        - [Using Combine](https://github.com/sungjunyoung/apache_beam_doc_ko/tree/master/1.%20Apache%20Beam%20Programming%20Guide/4.%20Applying%20transforms/3.%20Using%20Combine)
        - [Using Flatten and Partition](https://github.com/sungjunyoung/apache_beam_doc_ko/tree/master/1.%20Apache%20Beam%20Programming%20Guide/4.%20Applying%20transforms/4.%20Using%20Flatten%20and%20Partition)
        - [General Requirements for Writing User Code for Beam Transforms](https://github.com/sungjunyoung/apache_beam_doc_ko/tree/master/1.%20Apache%20Beam%20Programming%20Guide/4.%20Applying%20transforms/5.%20General%20Requirements%20for%20writing%20user%20code%20for%20Beam%20transforms)
        - [Side Inputs and Side Outputs](https://github.com/sungjunyoung/apache_beam_doc_ko/tree/master/1.%20Apache%20Beam%20Programming%20Guide/4.%20Applying%20transforms/6.%20Side%20Inputs%20and%20Side%20Outputs)
    - [Composite Transforms](https://github.com/sungjunyoung/apache_beam_doc_ko/tree/master/1.%20Apache%20Beam%20Programming%20Guide/5.%20Composite%20Transforms)
    - [Pipeline I/O](https://github.com/sungjunyoung/apache_beam_doc_ko/tree/master/1.%20Apache%20Beam%20Programming%20Guide/6.%20Pipeline%20Input%20Output)
    - [Running the Pipeline](https://github.com/sungjunyoung/apache_beam_doc_ko/tree/master/1.%20Apache%20Beam%20Programming%20Guide/7.%20Running%20the%20pipeline)
    - [Data Encoding and Type Safety](https://github.com/sungjunyoung/apache_beam_doc_ko/tree/master/1.%20Apache%20Beam%20Programming%20Guide/8.%20Data%20encoding%20and%20type%20safety)
    - Working with Windowing (준비중)
    - Working with Triggers (준비중)

- **[Pipeline Fundamentals](https://github.com/sungjunyoung/apache_beam_doc_ko/tree/master/2.%20Pipeline%20Fundamentals)**
    - [Design Your Pipeline](https://github.com/sungjunyoung/apache_beam_doc_ko/tree/master/2.%20Pipeline%20Fundamentals/1.%20Design%20Your%20Pipeline)
        - [What to consider when designing your pipeline](https://github.com/sungjunyoung/apache_beam_doc_ko/tree/master/2.%20Pipeline%20Fundamentals/1.%20Design%20Your%20Pipeline/1.%20What%20to%20consider%20when%20designing%20your%20pipeline)
        - A basic pipeline
        - Branching PCollections
            - Multiple transforms process the same PCollection
            - A single transform that uses side outputs
        - Merging PCollections
        - Multiple source
        - What's next
    - Create Your Pipeline
        - Creating Your Pipeline Object
            - Setting PipelineOptions from Command-Line Arguments
            - Creating Custom Options
        - Reading Data Into Your Pipeline
        - Applying Transforms to Process Pipeline Data
        - Writing or Outputting Your Final Pipeline Data
        - Running Your Pipeline
        - What's next
    - Test Your Pipeline
        - Testing Individual DoFn Objects
            - Creating a DoFnTester
            - Creating Test Inputs
                - Side Inputs and Outputs
            - Processing Test Inputs and Checking Results
        - Testing Composite Transforms
            - TestPipeline
            - Using the Create Transform
            - PAssert
            - An Example Test for a Composite Transform
        - Testing a Pipeline End-to-End
            - Testing the WordCount Pipeline
