Limiting to add a specific model from django admin
==================================================

The common way is handling it by setting permittions of user. But in
some cases, we need a limitation to add/change/delete a specific model
for all of users (such as limiting to add a AsyncTask model).

it's easy:

``` {.sourceCode .python}
class MyModelAdmin(admin.ModelAmdin):
    def has_add_permission(self, request):
        return False
```

forcible, a little. You can use this way for them too:

-   change: has\_change\_permission
-   delete: has\_delete\_permission

