## Payments Fraud Detection Examples

Source dataset: https://www.kaggle.com/datasets/rupakroy/online-payments-fraud-detection-dataset

The dataset provides a list of undated transactions between `NameOrig` and `nameDest` parties. For the purpose of the exercise, let’s assume that the data is sequential in time as it’s presented in the file. Assume  that all transactions happen inside an eclosed network, where `cash_out` transactions are payments, unless `NameOrig` == `nameDest` 

Examples using SQL (Snowflake), Graph (NewtorkX) or Data Frames (Pandas) 
- Build a network of all transactions and find top 5 “transaction subnets” in two ways: largest degree between participants and largest amounts of transactions performed between connected participants
- Find participants that serve as “cash out hubs”, top 10
- Find rings of transactional members where majority (80%) of the money transacted from a participant end up in the same account via a chain of transactions (A->B->C->…->A), get the top 10 by volumes
