# AWS SAA - 03
## AWS Backup
![image](https://github.com/user-attachments/assets/b1484b52-bdc3-438e-b1e7-d3ecb6a664e6)

![image](https://github.com/user-attachments/assets/3b7a66eb-0815-4fdc-913f-daafeef72acf)

![image](https://github.com/user-attachments/assets/ae8f5d9a-cb7f-4678-99d0-b6a1b39cac37)

![image](https://github.com/user-attachments/assets/656c5e38-ff53-4197-a8f7-c8933800a6dd)

![image](https://github.com/user-attachments/assets/c73142dd-3b7a-4fde-82e4-5b0ff4b2eb4b)

![image](https://github.com/user-attachments/assets/ef5c3e1a-15f2-43bd-b310-91071786ee2a)

![image](https://github.com/user-attachments/assets/5eae2c45-55b5-4087-840f-f7920d2738c8)

![image](https://github.com/user-attachments/assets/6d82ad92-dc59-47bd-ac7f-da5a6bf2e3b5)

![image](https://github.com/user-attachments/assets/0cb00bf0-a265-41a5-a73c-f3f0a5012098)

![image](https://github.com/user-attachments/assets/a1815208-8e91-49f8-bee5-b08473ab5fca)

![image](https://github.com/user-attachments/assets/0d9e4754-8269-4be2-b25e-99f395794160)

![image](https://github.com/user-attachments/assets/2604f98d-01ea-4520-a4a2-f09c7101ef42)

![image](https://github.com/user-attachments/assets/d8fe5295-ddc8-4ba6-b274-52f1bb4c146d)

Use the below templates to create RDS mysql , psql instances
<a href="https://github.com/ExamProCo/AWS-Examples/tree/main/dms/serverless"> DMS</a>

create the dms instance, migation tasks, s3 bucket and role, policies for the migartion activities. it is more complex.

after migation check the table present in database for database name;
```sh
SELECT table_schema, table_name
FROM information_schema.tables
WHERE table_schema NOT IN ('information_schema', 'mysql', 'performance_schema', 'sys');
```


![image](https://github.com/user-attachments/assets/ac9679a6-93b5-4eae-a9ab-414e0422ffd6)

![image](https://github.com/user-attachments/assets/95eb8dcb-feaa-46f2-8d29-35d207a47c05)

![image](https://github.com/user-attachments/assets/8e757ffb-545b-4cf8-aacf-4ef460ec5722)

![image](https://github.com/user-attachments/assets/6baffae4-0f75-4394-9f40-1ef95480b96a)

![image](https://github.com/user-attachments/assets/ad6ce477-33c3-4d25-9e6e-0f09766a7be0)

![image](https://github.com/user-attachments/assets/99790733-3bc4-4a25-8df7-f53c95f20fdb)

![image](https://github.com/user-attachments/assets/5b2b7e83-91f5-4b32-b589-f3eebd003227)














