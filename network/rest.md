# REST

[Representational State Tranfer](https://www.zhihu.com/collection/467101293). If an architecture fits REST principle, we call it RESTful.

*   **HTTP methods:**

    ```markdown
    GET（SELECT）：从服务器取出资源（一项或多项）。
    POST（CREATE）：在服务器新建一个资源。
    PUT（UPDATE）：在服务器更新资源（客户端提供改变后的完整资源）。
    PATCH（UPDATE）：在服务器更新资源（客户端提供改变的属性）。
    DELETE（DELETE）：从服务器删除资源。
    HEAD：获取资源的元数据。
    OPTIONS：获取信息，关于资源的哪些属性是客户端可以改变的。
    ```

    ```markdown
    GET /zoos：列出所有动物园
    POST /zoos：新建一个动物园
    GET /zoos/ID：获取某个指定动物园的信息
    PUT /zoos/ID：更新某个指定动物园的信息（提供该动物园的全部信息）
    PATCH /zoos/ID：更新某个指定动物园的信息（提供该动物园的部分信息）
    DELETE /zoos/ID：删除某个动物园
    GET /zoos/ID/animals：列出某个指定动物园的所有动物
    DELETE /zoos/ID/animals/ID：删除某个指定动物园的指定动物
    ```
*   **Filtering**:

    ```markdown
    ?limit=10：指定返回记录的数量
    ?offset=10：指定返回记录的开始位置。
    ?page=2&per_page=100：指定第几页，以及每页的记录数。
    ?sortby=name&order=asc：指定返回结果按照哪个属性排序，以及排序顺序。
    ?animal_type_id=1：指定筛选条件
    ```
* **Other**:
  * use OAuth2.0 framework.
  * response data formatting in JSON rather than XML.
* **Protocol**: choose HTTPS as protocol between user and API.
*   **Domain**: as can as possible depoly under specific domain name.

    ```markdown
    https://api.example.com
    ```

    If API is simple enought that no further extension, then put them under main domain).

    ```markdown
    https://example.com/api
    ```
*   **Versioning**: put the API version number into URL.

    ```markdown
    https://api.example.com/v1/
    ```
*   **Endpoint**: API stands for a resource, so no verbs in URL, should be nouns. Also, the enpoints should match the column name in databaser tables and using plural nouns.

    ```markdown
    https://api.example.com/v1/zoos
    https://api.example.com/v1/animals
    https://api.example.com/v1/employees
    ```
