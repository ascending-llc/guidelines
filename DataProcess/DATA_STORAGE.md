# ASCENDING Data Storage Guide

Guideline for cloud data storage standard

## Table of Contents

  1. [Naming](#naming)
  1. [Security](#security)

## Naming

 - **Raw Data Bucket**: Bucket for raw data that has no change at all.
 - **Processed Data Bucket**: Bucket for ETL output from original data source.
 - **DataSet** We always need two pair of data buckets one for production and the other for testing purpose for other team to access it.
    ```
    {db-type}-{application-name}-{environment(optional)}
    // Raw
    postgres-ascending

    // processed
    postgres-ascending-processed

    // production
    postgres-ascending
    postgres-ascending-processed

    // qa/staging
    postgres-ascending-qa
    postgres-ascending-qa-processed
    ```

## Security

    All the data storage has to be encrypted at REST. SOX compliance data has to be encrpyted at client side.
