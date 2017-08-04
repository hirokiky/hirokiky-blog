Search view with long long parameters
=====================================

At most of web application have the so-called `search view`. The view
get some request parameters and display some search results by using
these parameters.

A search view does not cause any side effects, so it should expect a GET
request. The URL will be like this:

    /search?code=R100100&code=R1001001

and the view will be like this:

``` {.sourceCode .python}
def search_studends_view(request):
    f = SearchForm(data=request.GET)

    students = None
    if f.is_valid():
        codes = f.cleaned_data['code']
        students = get_students(codes=codes)

    return HttpResponse(",".join(students))
```

(This is a sample code, so I have not verified the correctness of it)

In most cases, the GET search view seems good and enough. But
unfortunately, it have some limitations.

With long long parameter
------------------------

Let's consider a searching by using very long parameters. If you should
create a view that can be OR search by 1,000 birthdays...

The URL will be like this:

    /search?code=R100100&code=R100101&code=R100102&code=R102000&code=...

Then, what would happen? (You may have a doubt the requirement itself)

The header of this request will be very long too. so, It will couse
"Request URI too large (414)".

Gunicorn's maxcimum size of a request line is 4094 bytes by default.

-   <http://docs.gunicorn.org/en/latest/configure.html#limit-request-line>

At Nginx, it is 8k bytes.

-   <http://wiki.nginx.org/NginxHttpCoreModule#large_client_header_buffers>

If you use reverse proxying, you also should consider settings
proxy\_buffers, proxy\_buffer\_size.

だるい。

With POST
---------

Another solution, you can use POST request with search view.

But, if you make the honest, you will run into trouble caused browser
history backing. If you back to POST result, most browser will raise
warning (like 'Web page has expired').

A re-POSTing is handled as bad process to protect such as multiple
registration. Also, the POST results will not be cached, because a POST
request cause side effect in general. (Some browsers like Chrome,
Firefox will cache it. but IE will not so even if you provide
Cache-Controll header)

To avoid this, you should redirect to a GET result page after POSTed.
POSTed parameter will store to session, and GET page display by using
it.

like this:

``` {.sourceCode .python}
def search_studends_view(request):
    if request.method == 'POST':
        request.session['search_params'] = request.POST
        return HttpResponseRedirect('/search')
    else:
        try:
            params = request.session['search_params']
        except KeyError:
            params = {}
        f = SearchForm(data=params)

        students = None
        if f.is_valid():
            codes = f.cleaned_data['code']
            students = get_students(codes=codes)

        return HttpResponse(",".join(students))
```

(Yes, this is a just sample code)

It is not beautiful.

If you want to paginate these results you shuould get a page number from
GET parameter (puke).

And more
--------

Serializing and compressing GET parameters on client side may also be
good solution.

