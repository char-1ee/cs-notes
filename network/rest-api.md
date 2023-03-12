# REST API

[Representational State Tranfer](https://www.zhihu.com/collection/467101293). If an architecture fits REST principle, we call it RESTful.

### URI

Good REST APIs should include versioning to maintain backward compatibility. This means if changes are made from one version to another that could break the API, consumers have enough time to upgrade to the latest version. The most straightforward is to embed API version in URI:

* **/api/v1/movies** is better than /api/movies

Use “singular” name to denote a single document (a.k.a. object), e.g:

* **/api/v1/movies/{id}**&#x20;
* **/api/v1/movies/{id}/rating**

Use “plural” for collections and storing a new document

* **/api/v1/movies** is better than /api/v1/movie

Use “verb” for controller. A controller resource models a procedural concept. Controller resources are like executable functions, with parameters and return values, inputs, and outputs. E.g:

* **/api/v1/computers/{id}/shutdown**&#x20;
* **/api/v1/cameras/{id}/turn-left**

Use forward slash (/) to indicate hierarchical relationships. By convention, **{id}** right after each resources refer to their identifier.

* **/api/v1/movies/{id}/actors** is equivalent to **/api/v1/movies/{movie-id}/actors**
* **/api/v1/movies/{id}/title**

Use hyphens (-) to improve the readability of the URIs

* **/api/v1/movies/{id}/release-date** GOOD
* /api/v1/movies/{id}/releaseDate NOT GOOD
* /api/v1/movies/{id}/release\_date NOT GOOD

### Query

* Filter by fields: **GET /api/v1/movies?director="Christopher Nolan"**
* Paginate results with offset: **GET /api/v1/movies?limit=20\&offset=100**
* Paginate results with cursor: **GET /api/v1/movies?cursor="dXNlcjpXMDdRQ1JQQTQ="**
* Sort results by fields: **GET /api/v1/movies?sort\_by=-releaseDate**. We can use +/- for asc and desc

### Verbs

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

POST is usually the choice for controller resources, even if the payload is empty

* **POST /api/v1/computers/{id}/shutdown {}**&#x20;
* **POST /api/v1/cameras/{id}/turn-left {args}**

### Metadata

```json
{
  "data": {
    // Generic struct of the requested resource
    // can be null on failure
  },
  "metadata": {
    "nextCursor": "optional, for list API only",
    "message": "Something meaningful for human to read",
    "errorCode": "ERR123"
  }
}
```

### **Other**:

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

## References

{% embed url="https://restfulapi.net/" %}
