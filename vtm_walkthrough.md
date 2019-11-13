# VTM Walkthrough

We assessed commit `74e64e1ccb617c83ba1db4cbbb24a33051e169f8`
## Findings
!!! danger sqli

!!!

!!! note username enum

!!!
!!! danger CMD INJECTION

[vtm/views.py at 74e64e1ccb617c83ba1db4cbbb24a33051e169f8 · sethlaw/vtm · GitHub](https://github.com/sethlaw/vtm/blob/74e64e1ccb617c83ba1db4cbbb24a33051e169f8/taskManager/views.py#L806-L815)

```python
@csrf_exempt
def ping(request):
    
    data = ""
    if request.method == 'POST':
        ip = request.POST.get('ip')
        cmd = "ping -c 5 %s" % ip
        data = subprocess.getoutput(cmd)
        
    return render(request, 'taskManager/ping.html', {'data': data})
```
!!!

---

# Notes for you/your team

## Behavior

* What does it do? (business purpose)
> an intentionally vulnerable task manager
* Who does it do this for? (internal / external customer base)
> internal/external
* What kind of information will it hold?
>all the datas 
* What are the different types of roles?
>admin, pm, team member
* What aspects concern your client/customer/staff the most?
>user input/output, shared database, PII, data loss, authn/authz, lack of logging, persisent compromise

## Tech Stack

Language|files|blank|comment|code
:-------|-------:|-------:|-------:|-------:
CSS|17|1965|348|16368
JavaScript|27|1804|1875|10605
Python|53|500|157|1889
HTML|30|182|106|1507
JSON|7|1|0|768
SVG|2|0|0|643
Markdown|2|60|0|300
Bourne Shell|2|0|0|6
--------|--------|--------|--------|--------
SUM:|140|4512|2486|32086

* Framework & Language - Django/Python
* 3rd party components, Examples:
  
  ```
  django
  django_extensions
  mysqlclient
  ```
  Javascript 
  `taskManager/static/taskManager/js` 
* Datastore -  MySQL


## Brainstorming / Risks
* Database operations (CRUD), third party component
* Authn/Authz
* logging
* Data Loss/Leakage
* User Input 
* Out of Date 3rd Party Components 
  * jquery
  * bootstrap
* django templating (XSS)
* CSRF
* ping function (possible cmd injection)
* other cmd injections

## Checklist of things to review 

## Mapping / Routes

- [ ] /taskManager/search/
- [ ] /admin/auth/user/<id>/password/
- [ ] /taskManager/change_password/
- [x] /taskManager/forgot_password/
  * @csrf_exempt
  * no input validation on email
  * string concatenation
  * sqli
  * debug message
  * insecure token length and poor random
  * urandom?
  * token matched to user?
  * username enumeration on redirect
- [ ] /taskManager/reset_password/
- [ ] /taskManager/<project_id>/upload/
  * user auth to project 
  * proj.users_assigned.filter?
  * project creation
  * cursor execution?
  * input validation
  * cmd injection `taskManager/misc.py`
  * over writing files
  * write to sensitive path
  * file type validation
  * file size validation
- [X] /taskManager/ping
   * csrf exempt
   * not logged in (lack of access control)
   * !is_superuser (lack of access control)
   * Cmd injection
   * add whitelist
   * why does this exist? 
- [ ] /taskManager/profile/<user_id>

## Mapping / Authorization Decorators
`taskManager/views.py`
@login_required
@user_passes_test(can_create_project)
@user_passes_test(lambda u: u.is_superuser)
@csrf_exempt

### Authentication
- [ ] hashing/storage
- [ ] https
- [ ] cookie settings
- [ ] exempt csrf
- [ ] User identification
- [ ] Errors (User enumeration)
- [ ] brute force protection

### Authorization
- [ ] Lack of Access Control
- [ ] //TODO or hack 
- [ ] idor 
- [ ] is superuser functionality 
- [ ] create/delete projects
- [ ] unauthorized password change
- [ ] auth to projects when created
- [ ] 
[vtm/views.py at 74e64e1ccb617c83ba1db4cbbb24a33051e169f8 · sethlaw/vtm · GitHub](https://github.com/sethlaw/vtm/blob/74e64e1ccb617c83ba1db4cbbb24a33051e169f8/taskManager/views.py#L486-L486)
Lack of decorator
`taskManager/views.py`

```python
no decorator
def profile_view(request, user_id):
def dashboard(request):
def tm_settings(request):
def view_img(request):
```


### Auditing/Logging
- [ ] password changes/failed/successful
- [ ] passwords being logged
- [ ] admin functions logged 
- [ ] sensitive data logged
- [ ] env variables/secret storage

### Injection
- [ ] sql injection
- [ ] cmd injection
- [ ] XSS 
  * [vtm/base_backend.html at 74e64e1ccb617c83ba1db4cbbb24a33051e169f8 · sethlaw/vtm · GitHub](https://github.com/sethlaw/vtm/blob/74e64e1ccb617c83ba1db4cbbb24a33051e169f8/taskManager/templates/taskManager/base_backend.html#L56-L57)

```html
                        <span class="username"><i class="fa fa-user fa-fw"></i> {{ user.username|safe }}</span>

```


- [ ] file upload
- [ ] use of `os.` and `subprocess`

### Cryptography
- [ ] Password hashing
- [ ] base64?md5?sha1?

### Configuration
- [ ] defaults
- [ ] secret key
- [ ] search git for keys (trufflehog)
- [ ] verbose logging
- [ ] debug mode? 

## Mapping / Files

- [ ] taskManager/views.py
- [ ] taskManager/settings.py
- [ ] base.html
- [ ] message.html
- [ ] 
