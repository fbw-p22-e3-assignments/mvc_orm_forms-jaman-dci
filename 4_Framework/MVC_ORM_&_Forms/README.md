# Django MVC, ORM & Forms
## Topics covered

* URLconf 
* Templates 
* Views - Class-based and function-based
* ORM - Models(creation and migration), fields & Relationships
* Forms - Form building and model forms

## Goals
* You will create a website using Django that will serve as basis to replicate the features you defined so far using Python alone.
* In this first part, you will start the Django project and will define the paths, views and templates to display the menus and display the data.
* You will start using a database with Django, define some models in the ORM and apply the changes on the database.
* You will skip the part that involves user input for now and will be reusing the `loader.py` and `classes.py` files to let the views have access to the stock data.
* You will improve the `Item` model by adding new fields to store more information and you will create e new feature to edit the items in the database.
* You will add user input functionality to your website.
* This will involve using a custom user authentication system and a search input box.
* You will expand the current models of your Django project with additional related models.
* You will also implement a login/logout feature and restrict the access to some views.

## Preparation

Create a virtual environment, if you haven't done it already during the [Coding Standards](../../2_Python-Basics/13_Coding_Standards) milestone of this project.

Then install Django and set up the required paths, views and templates to provide on the web the same functionality you defined in the CLI query tool.

Add a new variable in your `settings.py` named `DATA_DIR` that defines the absolute path to the directory where the data is. Then, manually add the data files `personnel.json` and `stock.json` to that directory.

Copy the file `classes.py` to the root of the `stock` app inside your Django project. Then, copy [this modified version](loader.py) of the `loader.py` in the same directory.

Finally, in your `stock/views.py` use the loader as you did in your `query.py` in the CLI version of the querying tool.

**views.py**

```
from .loader import Loader

# Since we are not implementing the users part yet
# we can comment out this line or simply remove it.
# personnel = Loader(model="personnel")
stock = Loader(model="stock")
```

## Steps
### 1. Build the views

You will have to provide, at least, the following views or components:

- **a menu**

    Instead of creating an initial menu with links to the different pages and then a `back` button, it is better to define the menu as a common component of every page. Do that in a way that does not involve copy/pasting the menu on every template.

- **list all items**

    Shows all items by warehouse.

- **browse by category**

    This should use two views, one with the list of categories available and the other one with the list of items in that category. The former should have a link for each category pointing to its list of items. The latter should have a `back` button or link to go back to the list of categories.

- **search item**

    Although in this section you will not use forms, you can define a search page that takes the search term from a slug in the URL.


Additionally, you can add other improvements, such as:


- **browse by warehouse**

    Replicate the approach used in the **browse by category** section. Instead of showing the 5000 items in a single page, show first a list of warehouses and a link to a list of all items in a single warehouse.

- **add common filters**

    Add a secondary menu to both the views **list by warehouse** and **list by category** (but not on any other view). In this menu, show a list of all the `state` available. When the user clicks one of these options, it will open a new view that will show only the items with a particular `state` in that `warehouse` or `category`.

- **linking items**

    On the views that output an item name, show that name as a link to the search URL, so the user can jump to the search functionality from anywhere.

    > Try to do this defining your own template tag that automatically returns the HTML link.

### 2. Website home

Start by integrating the HTML you created in [Milestone 2. Internet and HTML](https://github.com/dci-python-course/python-projects-individual.V1.0/blob/main/1_Fundamentals/5_Internet-and-HTML.md) for the home of the website.

Set this page as the home (root endpoint) and add a link or a button inviting the user to explore the stock.

> HINT: define some `base.html` template from which every single page in your app can inherit. Then define a `home.html` template that inherits from `base.html` and shows the home content.

### 3. Stock home

Define another page that will be used as the starting page of the stock application.

This page will just have a title (`Our Stock` or something descriptive) and the menu options in the stock app (list items, search an item and browse by category).

> HINTS
>
> - use namespacing to tell apart the general `home` from the `stock:home`.
> - Take the opportunity to write the stock menu as an inclusion template tag so that you can reuse this menu later on, on every page in the stock app.
> - Leave empty links for now, you will add them when you implement each of their views.

### 4. List all items

Define a view to list all the items and add the link to the stock menu.

This view must include a section title (`Our Stock`) and the general stock menu at the top.

> HINTS:
>
> - Define a `stock:base.html` template that inherits from the general `base.html` and includes the `Our Stock` title and the stock menu. Then create a `stock:items-list.html` template that inherits from it.
> - Pass the `stock` object returned by the loader in the context.

### 5. Search an item

This feature will need two views. One will work similarly to the **list all items** view. In this case, do not show each warehouse, just a list of items in alphabetical order and without repeated names.

Each item should be linked to a second new view that searches that item in all warehouses and shows the same information you showed in the CLI querying tool.

> You can skip the final message `Maximum availability`, as this information is no longer relevant.

### 6. Browse by category

This feature also requires two views: one with the list of available categories in alphabetical and another one to list the items from that category.


### 7. Set up the ORM

Start by configuring Django to use the PostgreSQL database you used in the previous milestone of this project.

Then, tell Django to automatically define the models for this database and replace the file `stock/models.py` with the result.

Review the file and adapt it to your style guide (use black and then flake8). Remove also the `managed = False` statement to leave it as `True` (you will want Django to take care of the database from now on).

Then, create and run the migrations using the `--fake-initial` flag.

> You can find more information about this option in the official [Django documentation](https://docs.djangoproject.com/en/4.0/ref/django-admin/#cmdoption-migrate-fake-initial).

### 8. Use QuerySets

Replace the **search item** feature of the stock application with an ORM query that returns exactly the output in the same format and structure as before.

> Remember, the search must include these features:
>
> - Is case insensitive. The search `phone` returns the same as `pHoNe`.
> - It queries the combination of the `state` and `category` fields. The search `BlAck mIcrOphoNe` must return results.
> - It returns unique item names and the amount of times they are found in the database.
> - It is sorted alphabetically

Then, replace the feature **place an order** so that it removes the items from the database using ORM queries.

> HINT: you may want to set up a custom QuerySet Manager to automatically add the full name of the item to your queries.

### 9. Add more fields

Add the following fields to the `Item` model:

- **name**

    The combination `state + category` will not be the actual name of the items any more. Every item is different even if they share these properties and we want to be able to write a different name on each one.

    The name should be a new required field. Because you already have data in the table, you will need to add a default value to this field.

    Initially, you will populate this field with the `state + category`, so after you run the migration, open the Django shell and type a query to do so.

    > Because users will often be doing searches on this field, create it with an index.
    >
    > HINT: remove the custom Item manager that was annotating the full name.

- **dimensions**

    This field will store a JSON with the keys `height`, `width` and `depth`. The field is not required, but if there are values it should only accept (and require) all the previous keys.

    > An item must have either a JSON with all the keys (and only those) or no JSON at all.
    >
    > HINT: use validation to ensure the consistency of this field.

- **category** and **state**

    Redefine both as fields with choices.

    > HINT: You can get the list of choices buy running an ad-hoc query on the `Item` model.

### 10. Replace the item list with a ListView

Replace the current item list view with a generic view [ListView](https://docs.djangoproject.com/en/4.0/ref/class-based-views/generic-display/#listview) to use the models and show the data from the database.

> Explore the [pagination feature](https://docs.djangoproject.com/en/4.0/topics/pagination/#using-paginator-in-view).

### 11. Editing an item

On the list of all items, add a link to a new path with a new view that shows a form to edit an item and save the changes.

> Make sure all the validations mentioned before take place.
>
> Explore the generic view [UpdateView](https://docs.djangoproject.com/en/4.0/ref/class-based-views/generic-editing/#django.views.generic.edit.UpdateView).

### 12. Change the Authentication Model

Django comes with a built-in `User` model that has, among other fields, the `username` and `password`. Therefore, storing this information in the `Employee` model is not necessary.

Remove those fields and add a new one with a 1:1 relationship to the built-in `User` model.

> HINT: you may want to back-up the database at this point to avoid losing data.
>
> This migration may be a little complex, because you may be removing the primary key of the table and any references to it (`lead_by`) will need to be refactored as well.
>
> The cleanest approach may be to rename the model first (ex: `OldEmployee`) and run the migrations, then add the new `Employee` model and finally write some Python code to migrate the data from one structure to the other and create the required users in the process.

Once you have the new `Employee` model, create a `login` path and a form view to let the user enter some credentials and log into the private area.

Edit the project's `base.html` template and add a link to the login view if the user is not authenticated. If s/he is authenticated, show a link to the logout view.

Finally, restrict the access to the edit item form to only registered users.

> On the edit view, you should redirect users to the login view if they are not authenticated.
>
> Additionally, you can also hide the edit link/button when the user is not authenticated.


> **Bonus tasks**
>
> 1. Override the default LogoutView with your custom template.
>
> 2. Create a `profile` view, where the logged in user can see and modify her/his own data.

### 13. Employee Standard Working Hours

For each employee we want to know the weekly working hours.

Employees may work different times on different days of the week, so the system must store the day of the week, the starting time and the finishing time.

> HINT: use a choice field to store the day of the week as integers (starting with 0 on Monday, to match how Python's datetime module works).

An employee may work more than one time window in the same day and the system must allow to search available employees on a specific time of the week.

There are no constraints on the number of time windows an employee must or may have, but for every existing window all fields will be required (day of the week, start and end).

Then, write some ORM queries to update some of the existing employees with some sample working hours. For example:

**Juno**
```
Mon 09:00 - 14:00
Tue 10:00 - 14:00
Wed 09:00 - 14:00
Thu 09:00 - 14:00
Fri 09:00 - 14:00
```
**India**
```
Wed 14:00 - 19:00
Thu 14:00 - 19:00
Fri 14:00 - 19:00
Sat 09:00 - 17:00
```
**Tom**
```
Mon 14:00 - 19:00
Tue 14:00 - 19:00
Wed 14:00 - 19:00
Sun 09:00 - 17:00
```

Finally, create a `/stock/employees/` path that shows all employees and their working hours.

> **Bonus task**
>
> Add a view to allow searching employees working on specific times of the week.
>
> The view must have a form with two fields: `day of the week` and `time`. On submit, it should return all the employees working at that time.

### 14. Employee History of Edited Items

We also want to know the items each employee has edited using the edit item form. Keep in mind any employee can edit any number of items in the database.

The system must also store the date each user edited each item.

Once you have designed the models, run the migrations and:

- modify the edit form so that, on submit, saves the history using your models.
- modify the `stock/base.html` so that the last 5 edited items of the currently logged user appear in every page of the stock app.

> **Bonus task**
>
> Replicate this feature for the orders, so that we keep track of them in the database and show them to the user as a banner on every page of the stock app.

### 15. Search

Replace the list of links in the current `stock/search/` view with a form that provides a search input box.

The search should match any item which combination `state` and `category` contains or partially contains the search term. It should also be case insensitive.

This time, the search results should show the different items names (state + category) matching the search term and the amount of them available in all warehouses.

### 16. Placing Orders

Now, add a button for each item in the search results to let the user place an order.

This will involve showing a form with the name of the item and the amount desired.

The item field should be already populated with the selected item and should be read only. The `amount` field should have a maximum limit equal to the item's availability.

When the user submits the form, the items should be removed from the stock and the data should be written back into the file.

This time, don't implement a user system.

> HINTS:
>
> - Define a form `SearchForm` with the fields `item` and `amount`.
>
> - Use the `max` property of numeric HTML input fields.
>
> - Override the `get_form` method from the `FormView` to set the initial values of the item and the maximum amount.