---
layout: example
id: examples_datatable
title: Native JavaScript DataTable example
script: datatable
---

Bunny DataTable with all sub-components is only **5.4kb gzipped**.

Please note there are old `src/bunny.datatable` and `src/bunny.paginator` which are deprecated now. Use newest versions instead: `src/DataTable` and `src/Pagination`.

## Live demo

{% raw %}
    <datatable url="https://bunnyjs.com/api/users/" template="users_row_template">
        <header class="row">
            <div class="col-lg-4">
                <input name="search" class="form-control" placeholder="Search all...">
            </div>
            <div class="col-lg-4">
                <input name="email" class="form-control" placeholder="E-mail...">
            </div>
            <div class="col-lg-4">
                <select name="age" class="form-control">
                    <option value="">Age...</option>
                    <option value="1">< 20</option>
                    <option value="2">21-29</option>
                    <option value="3">30+</option>
                </select>
            </div>
        </header>
        <table class="table">
            <tr>
                <th pid="id">ID</th>
                <th>Photo</th>
                <th pid="name">Name</th>
                <th pid="email">email</th>
                <th pid="age">Age</th>
                <th>Actions</th>
            </tr>
        </table>
        <footer class="clearfix">
            <pagination limit="9" outer class="pagination"></pagination>
            <stats></stats>
        </footer>
    </datatable>

</div>

<table id="users_row_template" hidden>
    <tr>
        <td v="index"></td>
        <td><img av="src:picture" alt="Picture"></td>
        <td v="name"></td>
        <td v="email"></td>
        <td v="age"></td>
        <td>
            <a href="/users/edit/" class="btn btn-primary" data-handler="edit">Edit</a>
            <a href="/users/delete/" class="btn btn-danger" data-handler="remove">Delete</a>
        </td>
    </tr>
</table>

{% endraw %}

## Server requirements

Bunny Pagination implements Laravel's PHP framework pagination interface. Server on any ajax response should return JSON array with next keys:

```javascript
{
   "total": 50,
   "per_page": 15,
   "current_page": 1,
   "last_page": 4,
   "next_page_url": "http://laravel.app?page=2",
   "prev_page_url": null,
   "from": 1,
   "to": 15,
   "data":[
        {
            // Result Object
        },
        {
            // Result Object
        }
   ]
}
```

For more information about Laravel pagination go to official [documentation](https://laravel.com/docs/master/pagination). There is a JSON example at the bottom.

## Markup

DataTable should be wrapped within `<datatable>` tag.

It SHOULD contain:
- one `<table>` child
- and one `<pagination>` child.

It MAY contain:
- `<stats>` container to display some statistics about the records.


It MUST HAVE attributes:
- **url="string"** - ajax URL without any params, for example `/api/users` or `/api/users/` (with trailing slash) depending on your URL schema, just make sure there wouldn't be any redirects. Bunny Pagination uses `page` param to determine pages.
- **template="string"** - ID of Bunny Template used to draw each table row.

`<pagination>` MAY have those optional attributes:
- **limit="number"** - the total number of links to display in pagination including Previous and Next buttons. So if you want to display pages from 1 to 5 then this option should be set to 7 which is also a default value.
- **outer** - boolean attribute. If specified then pagination will have also links to First and Last pages. Default - false.

And finally `<table>` should have one row of `<th>` tags representing each column's title.

## Search and order by column

Inputs and selects with `name="{GET PARAM NAME}"` may be added within `<datatable>` which will add search=value GET param to AJAX request. Server may handle such requests and filter response data.

For sorting by any column ASC/DESC `pid` attribute should be added into table head row cell: `<th pid="{COLUMN NAME}"></th>`. Columns with such attribute will be clickable.

When sorting is active 2 new params will be added to AJAX requests:
- `order_by={COLUMN NAME}`
- `order_rule=asc/desc`

Active column will have `DataTable.Config.classNameAsc/Desc` class added, which by default is `arrow-up/down`. Class can be used, for example, to add UTF8 arrows using `:after`.

Example:
```html
<datatable url="/api/users/" template="users_row_template">
  <table class="table">
    <tr>
      <th pid="id">ID</th>
      <th>Photo</th>
      <th pid="name">Name</th>
      <th pid="email">email</th>
      <th pid="age">Age</th>
      <th>Actions</th>
    </tr>
  </table>
  <footer class="clearfix">
    <pagination class="pagination" limit="9" outer></pagination>
    <stats></stats>
  </footer>
</datatable>
```

## Template markup

Bunny DataTable uses Bunny Template to render each row. Because template's root tag will be table row `<tr>` template's tag should be `<table>` or template contents will be empty in browser.

It is recommended to put all templates at the bottom of the page.

Define a new table with ID specified in datatable's template attribute. You may also add `hidden` boolean attribute to hide the template from page. Anyway Bunny Template will remove all template contents on DOM ready.

```html
<table id="users_row_template" hidden>
  <tr>
    <td v="index"></td>
    <td><img av="src:picture" alt="Picture"></td>
    <td v="name"></td>
    <td v="email"></td>
    <td v="age"></td>
    <td>
        <a href="/users/edit/" class="btn btn-primary" data-handler="edit">Edit</a>
        <a href="/users/delete/" class="btn btn-danger" data-handler="remove">Delete</a>
    </td>
  </tr>
</table>
```

Currently full Bunny Template documentation available on [GitHub wiki page](https://github.com/Mevrael/bunny/wiki/Template).


## Scripts

Since DataTable uses BunnyJS Template to render rows, import both.

```javascript
import { Template } from 'bunnyjs/src/bunny.template';
import { DataTable } from 'bunnyjs/src/DataTable';
```

It is recommended to import ES6 modules and compile your JavaScript into one bundled file via Rollup.js, Webpack or something else.

However, there are also available dist versions. Please note that bunnyjs.com is not a CDN.

```html
<script src="https://bunnyjs.com/dist/utils-dom.min.js"></script>
<script src="https://bunnyjs.com/dist/ajax.min.js"></script>
<script src="https://bunnyjs.com/dist/template.min.js"></script>
<script src="https://bunnyjs.com/dist/pagination.min.js"></script>
<script src="https://bunnyjs.com/dist/url.min.js"></script>
<script src="https://bunnyjs.com/dist/datatable.min.js"></script>

<!-- icons plugin -->
<script src="https://bunnyjs.com/dist/utils-svg.min.js"></script>
<script src="https://bunnyjs.com/dist/spinner.min.js"></script>
<script src="https://bunnyjs.com/dist/datatable.icons.min.js"></script>

<!-- scroll to top plugin --> 
<script src="https://bunnyjs.com/dist/element.min.js"></script>
<script src="https://bunnyjs.com/dist/datatable.scrolltop.min.js"></script>
```

The last step is defining template row for DataTable:

```
Template.define('users_row_template', {
    edit: function edit(btn, user) {
        btn.href += user.index;
    },
    remove: function remove(btn, user) {
        btn.href += user.index;
        btn.addEventListener('click', function (e) {
            e.preventDefault();
            // show custom confirm popup
            // in this example we will just call native confirm()
            if (confirm('Are you sure?')) {
                window.location = btn.href;
            }
        });
    }
}, {
    format_data: function format_data(data) {
        data.name = data.name.first + ' ' + data.name.last;
        return data;
    }
});
```

## DataTable onRedraw event

Whenever new page is loaded all onRedraw callbacks are called.

Callbacks should be added using `DataTable.onRedraw(datatable, callback)`.

Callback receives only one argument - data already in JSON format.

```javascript
DataTable.onRedraw(document.getElementsByTagName('datatable')[0], function (data) {
    console.info('Switched to page ' + data.current_page);
    console.info('First user name is:' + data.data[0].name);
});
```



