Bucketed Search
===============

## About

BuckedSearch is a plug-in for the jQuery JavaScript library. It is a tool to allow standard text input fields to become input for searches across multiple data sets (buckets).

Each bucket can be set up individually to connect to different data sources. BucketedSearch currently supports either client side or server side filtering on each bucket. 

Client side searching can be achieved by loading the entire dataset into the 'data' attribute of the bucket. This is extremely useful for small data sets as it is extremely fast. Server side searching is achieved by specifying a URL for the bucket which is queried to retrieve the results. 

Bucketed Search was inspired by the Facebook search where you can type in one search term and it will search users, pages, applications, etc and list them in their own sections (buckets).

## Required

- jQuery 1.7+

## Installation

1) Download [jQuery](http://docs.jquery.com/Downloading_jQuery)

2) Download this plugin.

- [ZIP](https://github.com/adric-s/bucketed-search/zipball/master)
- [Clone in Windows](github-windows://openRepo/https://github.com/adric-s/bucketed-search)
- `git clone git://github.com/adric-s/bucketed-search.git`

3) Include files in your HTML. The minimum required for this plugin are:
```html
    <script src="/path/to/jquery.js" type="text/javascript"></script>
    <script src="/path/to/bucketed-search.js" type="text/javascript"></script>
```

4) Execute the plugin:
```javascript
    $(myElement).bucketedSearch(options);
```

## Options

### Root options

| Name                  | Type     | Default                         | Description |
| --------------------- | -------- | ------------------------------- | ----------- |
| defaultText           | string   | 'Search'                        | Text to display when the input is empty. |
| buckets               | array    | []                              | An array of the search buckets. |
| emptyMessage          | string   | 'Nothing matched your search'   | Message displayed when no buckets have any results. |
| initMessage           | string   | 'Start typing to search'        | Message displayed when no input is provided. |
| defaultRenderFunction | function | `function(row) { return row; }` | The default function used to render the results. |
| onSelect              | function | `function(row) {}`              | A function to run when anything is selected. |
| action                | object   | null                            | A link at the end of the drop down to perform an action. |

### Bucket options

| Name               | Type     | Default                     | Description |
| ------------------ | -------- | --------------------------- | ----------- |
| name               | string   | ''                          | Bucket title. |
| show               | boolean  | true                        | Load by default, if `false` the user needs to select to 'include' the bucket. |
| displayLength      | integer  | 5                           | Max number of rows to show, `false` for no limit. |
| url                | string   | null                        | The remote location to retrieve the data from. |
| queryParameter     | string   | 'q'                         | The parameter to add the search term to the `url`. |
| delay              | integer  | 50                          | The delay from the last key press before running the query. Set longer delays to reduce server load by only querying once the user stops typing. |
| cacheResults       | boolean  | true                        | Use previously retrieved results before doing an AJAX call. |
| initializeData     | boolean  | false                       | If `true` the `url` is assumed to return all the data so that searching can be done on the client side. |
| data               | array    | null                        | All objects in the bucket. Used for small datasets. |
| searchFields       | array    | null                        | Which fields to search in the `data`. Used when `data` is an array of objects or `cacheResults` is true and the url returns a list of objects. |
| showOnEmptyQuery   | boolean  | false                       | If set to `true` when there is no search query it will show all results. |
| renderFunction     | function | The `defaultRenderFunction` | Function used to render each row. |
| onSelect           | function | `function(row) {} `         | The function to run when a row is selected. |
| extendedSearchUrl  | string   | null                        | A URL to link to to see a full list of results for the query. |
| extendedSearchText | string   | 'See More'                  | The text to display when an `extendedSearchUrl` is provided. |

**Note**: If the `url` is set and `initializeData` is `false` the `data` attribute will be ignored. If `initializeData` is `true` or `data` is given then the `queryParameter` and `delay` will be ignored.

### Action options

| Name     | Type     | Default             | Description                           |
| -------- | -------- | ------------------- | ------------------------------------- |
| name     | string   | ''                  | The text of the link.                 |
| onSelect | function | function(action) {} | What to do when someone clicks on it. |

## Methods

Once a bucketed search has been initialized on an element you can directly access bucketed search functions:
```javascript
    $(myElement).bucketedSearch('functionName', param1, param2, etc);
```

| Function     | Parameters | Description |
| ------------ | ---------- | ----------- |
| show         | none       | Shows the bucketed search drop down as though the user clicked on the input. |
| hide         | none       | Hides the bucketed search drop down as though the user clicked off the input. |
| getBuckets   | none       | Returns the bucket objects in an array. |
| update       | none       | Conducts searches across all buckets as though the user typed a character. |
| updateBucket | bucket     | Conducts a search only on a specific bucket. |
| select       | bucket, itemIndex | Select the row from the given bucket identified by the itemIndex (starting from 0) as through the user clicked on that item. |
| clearCache   | bucket (optional) | Clears the cache of the given bucket. If no buckets are given it will clear the cache for all buckets. |
| destroy      | none       | Remove the bucketed search plugin from the input element. |

**Example:**

```javascript
    $(myElement).val(newValue).bucketedSearch('show').bucketedSearch('update');
```

## Usage

Here's an example of a single search input field that will search through a local list of friends, do a server side search for all people and another server side search for any places that match the search term.

There are more options that are available that this doesn't demo but it covers the main ones.

```html
    <input id="search" type="text" />
    <script>
        var friends = [
            {
                'first_name': 'Joe',
                'last_name': 'Blogs',
                'age': 26
            },
            {
                'first_name': 'Jane',
                'last_name': 'Smith',
                'age': 21
            },
            {
                'first_name': 'John',
                'last_name': 'Smith',
                'age': 32
            }
        ];
        
        var renderPerson = function(person) {
            return person.first_name + ' ' + person.last_name;
        }
        
        $('#search').bucketedSearch({
            buckets: [
                {
                    name: 'Friends',
                    displayLength: 6,
                    data: friends,
                    searchFields: [ 'first_name', 'last_name' ],
                    renderFunction: renderPerson,
                    onSelect: function(person) {
                        // do something
                    }
                },
                {
                    name: 'People',
                    url: 'http://www.example.com/ajax/searchPeople.php',
                    cacheResults: true,
                    searchFields: [ 'first_name', 'last_name' ],
                    renderFunction: renderPerson,
                    onSelect: function(person) {
                        // do something
                    }
                },
                {
                    name: 'Places',
                    url: 'http://www.example.com/ajax/searchPlaces.php',
                    cacheResults: false,
                    delay: 100,
                    renderFunction: function(place) {
                        var html = '<div class="place-result">';
                        html += '<span class="name>' + place.name + '</span>';
                        html += '<span class="address">' + place.address + '</span>';
                        html += '</div>';
                        return html;
                    },
                    onSelect: function(place) {
                        // do something
                    },
                    extendedSearchUrl: 'http://www.example.com/places.php',
                }
            ]
        });
    </script>
```
