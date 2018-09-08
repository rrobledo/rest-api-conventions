This Document describes a list of conventions and best practices to build REST APIs.

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

