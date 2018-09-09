This Document describes a list of conventions and best practices to build REST APIs.


## Use Consistently Plural Nouns
Prefer
```
/employees
/employees/21
```
over
```
/employee
/employee/21
```

Indeed, it’s a matter of taste, but the plural form is more common. Moreover, it’s more intuitive, especially when using GET on the collection URL (GET /employee returning multiple employees). But most important: avoid mixing plural and singular nouns, which is confusing and error-prone.


## HTTP Methods

Use HTTP Methods to Operate on your Resources
```
GET /employees
GET /employees?state=external
POST /employees
PUT /employees/56
```
Use URLs to specify the resources you want to work with. Use the HTTP methods to specify what to do with this resource. With the five HTTP methods GET, POST, PUT, PATCH and DELETE you can provide CRUD functionality (Create, Read, Update, Delete) and beyond.

```
Read: Use GET for reading resources.
Create: Use POST or PUT for creating new resources.
Update: Use PUT and PATCH for updating existing resources.
Delete: Use DELETE for deleting existing resources.
```

Understand the Semantics of the HTTP Methods  
*Definition of Idempotence*: A HTTP methods is idempotent when we can safely execute the request over and over again and all requests lead to the same state.

**GET**
- Idempotent
- Read-only. GET never changes the state of the resource on the server-side. It - must not have side-effects.
- Hence, the response can be cached safely.
- Examples:
    ```
    GET /employees - Lists all employees
    GET /employees/1 - Shows the details of the employee 1
    ```

**PUT**
- Idempotent!
- Can be used for both creating and updating
- Commonly used for updating (full updates).
    - Example: 
    ```
    PUT /employees/1 - updates employee 1 (uncommon: creates employee 1)
    ```
- To use PUT for creating, the client needs to know the whole URL (including the ID) upfront. That’s uncommon as the server usually generates the ID. So PUT for creating is typically used when there is only one element and the URL is unambiguous.
    - Example: 
    ```
    PUT /employees/1/avatar - creates or updates the avatar of employee 1. 
    ```
    There is only one avatar for each employee.
- Always include the whole payload in the request. It’s all or nothing. PUT is not meant to be used for partial updates (see PATCH).

**POST**
- Not idempotent!
- Used for creating
- Example: 
    ```
    POST /employees 
    ```
    creates a new employee. The new URL is delivered back to the client in the Location Header (e.g. Location: /employees/12). Multiple POST requests on /employees lead to many new different employees (that’s why POST is not idempotent).

**PATCH**
- Idempotent
- Used for partial updates.
- Example: 
    ```
    PATCH /employees/1
    ```
    updates employee 1 with the fields contained in the payload. The other fields of employee 1 are not changed.

**DELETE**
- Idempotent
- Used for deletion.
- Example: 
    ```
    DELETE /employees/1
    ```


## Data Representation

### Attribute names

Use JSON as default. Follow JavaScript conventions for naming attributes

- Use medial capitalization (aka CamelCase)

- Use uppercase or lowercase depending on type of object

This results in code that looks like the following, allowing the JavaScript developer to write it in a way that makes sense for JavaScript.

Example:
```
{
    "name": "Juan Perez",
    "address": "Some Place",
    "createdAt": 1320296464
}
```

var user = JSON.parse(response);

console.log(user.createdAt);

### DateTime representation

JSON itself does not specify how dates should be represented.

Epoch = 1511324473  
iso8601 =   2017-11-22T04:21:13Z

The benefit of epoch like is that it is smaller and will be faster to process in your program. The downside is it means nothing to humans. But take in account that you need to use 64-bit integer for the epoch seconds since 32 bits will overflow in year 2038.


iso8901 dates are easy to read on their own and don't require the user to translate a number in to a recognizable date. The size increase in iso8601 is unnoticeable when compared to much much larger things like images.

**I recommend you to use iso8601** since it use the format emitted by Date's toJSON method and:

- it's human readable but also succinct
- It sorts correctly
- It includes fractional seconds, which can help re-establish chronology
- It conforms to ISO 8601
- ISO 8601 has been well-established internationally for more than a decade
- ISO 8601 is endorsed by W3C, RFC3339, and XKCD

### Partial response

Used to give developers just what they need in responses, The idea is add optional **fields** in a comma delimited list

Example:

```
Person
{
    "@type": "Person",
    "name": "Juan Perez",
    "address": {
    "@type": "PostalAddress",
    "addressLocality": "Colorado Springs",
    "addressRegion": "CO",
    "postalCode": "80840",
    "streetAddress": "100 Main Street"
    },
    "email": "mailto:info@example.com",
    "image": "juanperez.jpg",
    "jobTitle": "Research Assistant",
    "gender": "Male",
    "telephone": "(123) 456-6789",
}
```

```
PROJECTION:  
/customers?fields=name,telephone,address

RESULT:  
Person
{
    "@type": "Person",
    "name": "Juan Perez",
    "address": {
        "@type": "PostalAddress",
        "addressLocality": "Colorado Springs",
        "addressRegion": "CO",
        "postalCode": "80840",
        "streetAddress": "100 Main Street"
    },
    "telephone": "(123) 456-6789",
}

PROJECTION:  
/customers?fields=name,telephone,address.streetAddress

RESULT:  
Person
{
    "@type": "Person",
    "name": "Juan Perez",
    "address": {
        "@type": "PostalAddress",
        "streetAddress": "100 Main Street"
    },
    "telephone": "(123) 456-6789",
}

```

### Pagination 

The pagination defaults are of course dependent on your data size. If your resources are large, you probably want to limit it to fewer than 10; if resources are small, it can make sense to choose a larger limit.

Use limit and offset


