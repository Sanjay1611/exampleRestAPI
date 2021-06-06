# Rest API in golang

## Introduction
This a rest API which can hold a booking for the guests for a party
### Scope
- Booking for guests
- Arriving (when a guest arrives)
- Leaving (when a guest leaves)

## Database

There will be 2 tables present. 
- Guests - This will keep track of all guests who have booked, arrived or left after arriving
  This table has 4 columns
  - table_number - Table at which guest is sitting. This will also referenece table_number field of collection `Table`. However reference is not made in the database explicitly as `Table.table_number` is a constant field 
  - name - Name of the guest (assumed unique)
  - accompanying guests - Number of guests accompanied by this guest
  - time_arrived - Arrival time of the guest
  - id - random unique id (auto incemented field of sql)
  - created_at - creation time of this row. Can be treated as booking time.
  - updated_at - last updation time
  - deleted_at - deletion time of the row. This field will be populated when a guest leaves i.e When a guest leaves he/she will be deleted(soft delete) from table data and will not be listed in any of the API (according to the scope of this API however can be enhanced later)
- Tables - This will be a sort of constant table which will contain the record of tables available in the venue and their capacity(assuming capacity of all tables will be same initially).
  This table has 2 columns
  - table_number - Unique number assigned to each table (auto incremented field of sql).
  - seats_empty - Will denote the number of seats left empty at a table at any point of time in the party.

```
-----------------------                   ----------------
|       Guests        |                   |    Tables    |
-----------------------                   ---------------
|    table_number     | ---References-->  | table_number |
|        name         |                   | seats_empty  |
| accompanying_guests |                   ----------------
|    time_arrived     |
|   id(primarykey)    |
|     created_at      |
|     updatd_at       |
|     deleted_at      |
-----------------------
```

## API

- `/guest_list/<name>` [POST] - Book a table with the given name.
Request Payload
```
{
    "table": int,
    "accompanying_guests": int
}
```
Response Payload
```
{
    "name": "string"
}
```

- `/guest_list` [GET] - Get the guest list (will exclude the guests who have already left).
Response Payload
```
{
    "guests": [
        {
            "name": "string",
            "table": int,
            "accompanying_guests": int
        }, ...
    ]
}
 ```
- `/guests/<name>` [PUT] - A guest with the name <name> has arrived.
  Request Payload
  ```
  {
    "accompanying_guests": int
  }
  ```
  Response Payload
  ```
  {
    "name": "string"
  }
  ```
- `/guests` [GET] - Get the list of arrived guests (excludes guests who have already left).
  Response Payload
  ```
  {
    "guests": [
        {
            "name": "string",
            "accompanying_guests": int,
            "time_arrived": "string"
        }
    ]
  }
  ```
- `/guests/<name>` [DELETE] - A guest has left (will soft delete the guest).
- `/seats_empty` [GET] - Will tell the total number of seats left(of all the tables combined).
  Response Payload
  ```
  {
    "seats_empty": int
  }
  ```
  
## TechStack  
  
  - Format - Rest API
  - Language - Golang 1.14
      - Gorm Library for Database Operations
      - SQL Driver - mysql driver of gorm
  - Database - SQL 5.7
  
## Usage 
  - Create a database `party` in your sql server
  - Run the program as 
  ```
  ./main -dsn <dsn> -t <number_of_tables> -c <capacity_per_table>
  ```
  Flags
  - `-dsn` - It will specify the dsn of the sql server for your system. By default it will take the local mysql server `root:root@/party`. you can change the database name accordingly and pass its dsn here.
  - `-t` - It will specify the number of tables at the venue. By default this will be 10.
  - `-c` - It will specify the capacity of each table(assuming the capacity will be same for each table).






