---
description: Page + Offset, Cursor-based
---

# API pagination

### Page + Offset



`LIMIT` specifies the maximum number of rows to return in the result set. For example, if you set `LIMIT` to 10, the result set will contain a maximum of 10 rows.

`OFFSET` specifies the number of rows to skip before beginning to return rows. For example, if you set `OFFSET` to 20 and `LIMIT` to 10, the result set will contain rows 21-30.

How offset and limit match pgno and pgsz:

* Page number: The page number represents the current page you want to display. For example, if you're displaying the third page, the page number would be 3.
* Page size: The page size represents the number of rows you want to display on each page. For example, if you're displaying 20 rows per page, the page size would be 20.
* OFFSET: The `OFFSET` value is calculated by multiplying the page number minus 1 by the page size. For example, if the page size is 20 and you want to display page 3, the `OFFSET` value would be 40 (i.e., `OFFSET` = (3 - 1) \* 20).
* LIMIT: The `LIMIT` value is simply set to the page size. For example, if the page size is 20, the `LIMIT` value would be 20.

#### Update

Noticed that response should include field `count`, which indicate how many records existing in DB so that FE can use that \`count\` with pgsz and pgno for rendering.

#### Update (30/11/2024)

Page-based

<div align="left"><figure><img src="../.gitbook/assets/pg (2).jpg" alt="" width="287"><figcaption></figcaption></figure></div>

Offset-based&#x20;

<div align="left"><figure><img src="../.gitbook/assets/image (31).png" alt="" width="278"><figcaption></figcaption></figure></div>

```sql
SELECT * FROM table LIMIT 10 OFFSET 20;
```

However it might perform bad for large scale and rapid changing dataset.
