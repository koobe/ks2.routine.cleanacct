###清除特定站台的使用者帳戶資料
=====================

I. 用 domain name 查詢要清除帳號的站台的 TenanId：
```
SELECT *
FROM kbServiceTenantTbl
WHERE (DomainName LIKE '%?%')
```

II. 根據 TenanId 列出該站台所有使用者帳號 (排除admin帳號或其他預設帳號)：
```
SELECT * FROM kbUserListTbl WHERE (TenantId = ?) and LoginId <> 'admin'
```

III. 刪除使用者在資料表 kbUserBookListTbl 的資料：
```
WITH q AS (
SELECT *
FROM kbUserBookListTbl
WHERE (UserId IN (SELECT UserId FROM kbUserListTbl WHERE (TenantId = ?) AND (LoginId <> 'admin')))
) DELETE FROM q
```

III. 刪除使用者在資料表 kbGroupUserTbl 的資料：
```
WITH q as (
SELECT *
FROM kbGroupUserTbl
WHERE (UserId IN (SELECT UserId FROM kbUserListTbl WHERE (TenantId = ?) AND (LoginId <> 'admin')))
) DELETE FROM q
```

V. 刪除使用者資料 kbServiceTenantTbl：
```
WITH q AS (
SELECT * FROM kbUserListTbl WHERE (TenantId = ?) and LoginId <> 'admin'
) DELETE FROM q
```
