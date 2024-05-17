# How to store historical data



* Audit table -> store changed fields and timestamps row by row
* History table -> store the all odd and new historical data into one table
* Chain table&#x20;

```sql
CREATE TABLE historical_data (
  id INT AUTO_INCREMENT PRIMARY KEY,
  original_id INT NOT NULL,
  data1 VARCHAR(255),
  data2 INT,
  data3 DATE,
  change_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  user VARCHAR(255),
  notes VARCHAR(255)
);
```

Whenever a change is made to the original table, you can insert a new record into the `historical_data` table to store the historical data. For example:

```sql
INSERT INTO historical_data (original_id, data1, data2, data3, user, notes)
VALUES (123, 'new value', 42, '2023-02-14', 'John Doe', 'Updated data1 and data2');
```

Here, `123` is the primary key of the original record that was changed, and the other values represent the new data, the user who made the change, and any notes about the change.

This is like a linked list that changed records are regarded as totally new records, and linked to old records by `original_id` . Thus, no need to modify the original table.

By creating a separate table to store historical data, you can maintain a complete record of changes over time without modifying the original table. You can also query the `historical_data` table to see how data has changed over time and who made the changes.

### References

{% embed url="https://dev.to/zhiyueyi/design-a-table-to-keep-historical-changes-in-database-10fn" %}
