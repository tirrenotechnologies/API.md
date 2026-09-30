# tirreno API reference

`tirreno('…')` is tirreno's built-in API. It gives your own code the same building blocks the console uses: the current request and page, the logged-in operator, tracked entities and events, detection rules, logging and utilities. Use it to add your own pages to the console and build your own application on tirreno.

## Terms

| Term | Meaning |
|---|---|
| Console | tirreno's web interface (dashboard, users, rules, settings) where operators review activity |
| Operator | A person who logged in to the tirreno console |
| User | An end user of your product, created from events sent to `/sensor/` |
| API key | One monitored product. All tracked data belongs to a key, and the console works on one key at a time |
| Rule | A detection rule such as `I01` ("IP belongs to TOR"). Each key can set its own `value`; a user's `score_details` lists the rules that matched |

## Services

| Name | Return | Summary |
|---|---|---|
| `tirreno('page')` | `Services\Page` | The page being rendered: title, template, template variables, allowed roles |
| `tirreno('request')` | `Services\Request` | The current HTTP request: method, path, parameters, headers, client IP, CSRF check |
| `tirreno('response')` | `Services\Response` | Redirects, HTTP errors, and login/role guards |
| `tirreno('session')` | `Services\Session` | The console login session: current operator, active API key, session values |
| `tirreno('sysop')` | `Services\Sysop` | The logged-in operator's record with roles (guest operator if not logged in) |
| `tirreno('helpers')` | `Services\Helpers` | View helpers |
| `tirreno('queries')` | `Services\Queries` | Query builders over the current key's tracked data |
| `tirreno('users')` | `Services\Users` | Query builder over tracked users (same as `$queries->users`) |
| `tirreno('ips')` | `Services\Ips` | Query builder over IP addresses (same as `$queries->ips`) |
| `tirreno('user')` | `Services\User` | Loads one tracked user by id |
| `tirreno('ip')` | `Services\Ip` | Loads one IP address by id |
| `tirreno('rules')` | `Services\Rules` | Lists detection rules for the current key or for one user |
| `tirreno('rule')` | `Services\Rule` | Loads one detection rule |
| `tirreno('entities')` | `Services\Entities` | Access to the entity classes (`User`, `Ip`, `Rule`, …) |
| `tirreno('db')` | `Services\Db` | Opens the PostgreSQL connection |
| `tirreno('storage')` | `Services\Storage` | The framework's global key-value store (config and request data) |
| `tirreno('router')` | `\Base` | The Fat-Free Framework instance |
| `tirreno('log')` | `Services\Log` | Application log |
| `tirreno('constants')` | constants proxy | Application constants as properties |
| `tirreno('utils')` | `Services\Utils` | Utility classes and current time |
| `tirreno('assets')` | `Services\Assets` | Bundled detection lists and the custom-page menu |
| `tirreno('models')` | `Services\Models` | SQL data-access classes behind the console |
| `tirreno('grids')` | `Services\Grids` | Data sources of the console's tables |
| `tirreno('charts')` | `Services\Charts` | Data sources of the console's charts |
| `tirreno('controllers')` | `Services\Controllers` | Logic behind each console section |
| `tirreno('pages')` | `Services\Pages` | The built-in console pages |

## $page

| Name | Return | Summary |
|---|---|---|
| `$page->setTitle(string $title)` | void | Set the page title. In a file-based page, a plain string here is also the menu label |
| `$page->getTitle()` | ?string | Page title (`Default title.` if not set) |
| `$page->addParams(array $params)` | void | Add template variables; existing keys are overwritten |
| `$page->setParams(array $params)` | void | Replace all template variables |
| `$page->getParams()` | array | All template variables |
| `$page->setTemplate(string $file)` / `getTemplate()` | void / ?string | Template file |
| `$page->setJavascript(string $file)` / `getJavascript()` | void / ?string | Page script file |
| `$page->setName(string $name)` / `getName()` | void / ?string | Page name (`DefaultName` if not set) |
| `$page->setView(string $view)` / `getView()` | void / ?string | View type, e.g. `json` |
| `$page->setAuthenticated(bool $value)` / `getAuthenticated()` | void / bool | Login-required flag of class-based pages (file-based pages run for guests; use `$response` guards) |
| `$page->setAllowedRoles(array $roles)` / `getAllowedRoles()` | void / array | Allowed roles: a list, or per HTTP method (`['GET' => [...], 'POST' => [...]]`). Empty → `['operator']` |
| `$page->setBlockedRoles(array $roles)` / `getBlockedRoles()` | void / array | Roles that are denied (same forms) |

## $request

| Name | Return | Summary |
|---|---|---|
| `$request->getRequestType()` | ?string | HTTP method: `GET`, `POST`, … |
| `$request->isGet()` / `isPost()` / `isPut()` / `isDelete()` | bool | Check the HTTP method |
| `$request->isAjax()` | bool | AJAX request (`X-Requested-With: XMLHttpRequest`) |
| `$request->isCli()` | bool | Running from the command line (cron) |
| `$request->isHttps()` | bool | Scheme is HTTPS |
| `$request->getUri()` | string | Full URI with query string |
| `$request->getPath()` | string | Path without query string |
| `$request->getQuery()` | ?string | Raw query string |
| `$request->getFragment()` | ?string | URL fragment (browsers don't send it, so normally `null`) |
| `$request->getPattern()` / `getRouteAlias()` | string / ?string | Matched route pattern / route alias |
| `$request->getIp()` | ?string | Client IP |
| `$request->getUserAgent()` | string | Client user agent |
| `$request->getHeaders()` | array | All request headers |
| `$request->getHeader(string $name)` | ?string | One header |
| `$request->getContentType()` | ?string | `Content-Type` header |
| `$request->contentTypeIsJson()` / `contentTypeIsUrlEncoded()` | bool | Check the content type |
| `$request->getGet()` / `getPost()` | ?array | Query / form parameters |
| `$request->getBody()` | array\|string\|null | Raw body |
| `$request->getAllPayload()` | array | GET, POST and JSON body merged |
| `$request->getRequestParam(string $key)` | mixed | One value from the merged payload, or `null` |
| `$request->getStringRequestParam(string $key, bool $nullable = true)` | ?string | Value as string; missing → `null` (`''` if `$nullable` is false) |
| `$request->getIntRequestParam(string $key, bool $nullable = true)` | ?int | Value as int; missing → `null` (`0`) |
| `$request->getArrayRequestParam(string $key, bool $nullable = true)` | ?array | Array values re-indexed; not an array → `null` (`[]`) |
| `$request->getDictionaryRequestParam(string $key, bool $nullable = true)` | ?array | Array with keys kept; not an array → `null` (`[]`) |
| `$request->getUrlParams()` | array | Route parameters; index `0` is the matched path (`[0 => '/verify']` in a file-based page) |
| `$request->getUrlParam(string $key)` | ?string | One route parameter, or `null` |
| `$request->getStringUrlParam(string $key, bool $nullable = true)` | ?string | Route parameter as string; missing → `null` (`''`) |
| `$request->getIntUrlParam(string $key, bool $nullable = true)` | ?int | Route parameter as int; missing → `null` (`0`) |
| `$request->validateCsrf()` | int\|false | `false` if the form field `token` matches `$session->get('csrf')`, otherwise `601` |
| `$request->resetPayloadCache()` | void | Re-read the payload after it changed |
| `$request->setTimer()` / `getTimer(int $idx = 0)` | int / ?float | Start a timer / seconds since it started |

## $response

| Name | Return | Summary |
|---|---|---|
| `$response->redirect(string $route = '/')` | void | Redirect to a route |
| `$response->error(int $code = 403)` | void | Stop with an HTTP error page; AJAX requests get JSON `{status, code, message}`. On a normal page, `403` redirects to `/logout` |
| `$response->redirectNotLoggedIn(string $route = '/')` | void | Redirect if the operator is not logged in |
| `$response->redirectLoggedIn(string $route = '/')` | void | Redirect if the operator is logged in |
| `$response->redirectImproperRole(array $allowed, array $blocked = [], string $route = '/')` | void | Redirect unless the operator has an allowed role and no blocked role |
| `$response->redirectProperRole(array $allowed, array $blocked = [], string $route = '/')` | void | Redirect if the operator has an allowed role and no blocked role |
| `$response->errorNotLoggedIn(int $code = 401)` | void | Error (401) if the operator is not logged in |
| `$response->errorImproperRole(array $allowed, array $blocked = [], int $code = 401)` | void | Error (401) unless the operator has an allowed role and no blocked role |

## $session

| Name | Return | Summary |
|---|---|---|
| `$session->getCurrentOperator()` | `Entities\Operator` | Logged-in operator, or the guest operator. `->isGuest()`, `->isLoggedIn()`, `->roles` |
| `$session->getCurrentKey()` | ?`Entities\ApiKey` | Active API key (`->id`) |
| `$session->get(string $key)` | mixed | Read a session value |
| `$session->set(string $key, mixed $value)` | mixed | Store a session value |
| `$session->remove(string $key)` | mixed | Delete a session value |
| `$session->clear()` | void | Delete all session values (logs the operator out) |
| `$session->setActiveOperator(int $id)` | void | Set the session's operator; apply with `extractCurrentOperator()` |
| `$session->extractCurrentOperator()` | void | Load the operator and the API key (`active_key_id`) from the session |

## $sysop

| Name | Return | Summary |
|---|---|---|
| `$sysop->id` / `->email` / `->timezone` / `->roles` | int / string / string / array | Logged-in operator's record |
| `$sysop->hasRole(string $role)` | bool | Operator has the role |
| `$sysop->isSuperuser()` | bool | Operator is a superuser |

## $helpers

| Name | Return | Summary |
|---|---|---|
| `$helpers->formatTitle(string $title)` | string | HTML-escaped title with the ` \| tirreno` suffix |

## $queries, $users, $ips

| Name | Return | Summary |
|---|---|---|
| `$queries->users` | `Query\Users` | Tracked users with their latest email and phone |
| `$queries->ips` | `Query\Ips` | IP addresses with ISP and country |
| `$queries->devices` | `Query\Devices` | Devices with parsed user agent |
| `$queries->sessions` | `Query\Sessions` | User sessions |
| `$queries->countries` | `Query\Countries` | Countries seen for this key, with totals |
| `$queries->referers` | `Query\Referers` | Referrers |
| `tirreno('users')` / `tirreno('ips')` | builder | Same as `$queries->users` / `$queries->ips` |

`Query\` = `\Tirreno\Models\Query\`. Builders only see the current key's data; for a guest, `$queries` builders return no rows.

### Builder methods

| Name | Return | Summary |
|---|---|---|
| `->where(string $column, string $op, $value = null)` | self | Add an AND condition |
| `->andWhere(string $column, string $op, $value = null)` | self | Same as `where()` |
| `->orWhere(string $column, string $op, $value = null)` | self | Add an OR condition. Conditions join in order without parentheses: `A OR B AND C` = `A OR (B AND C)` |
| `->whereColumn(string $column, string $op, string $column2)` | self | Compare two columns |
| `->orderBy(string $column, string $direction)` | self | Sort `ASC` or `DESC` (any case) |
| `->limit(int $n)` | self | Maximum rows |
| `->offset(int $n)` | self | Skip rows |
| `->find(string $selector)` | object | Run with a selector: `'user_userid=alice\|bob, sort=-user_lastseen, limit=20, start=40'` (`=` only, `\|` = OR, `-` = DESC) |
| `->getFields()` | array | Column → alias map |
| `->get()` | object | Run the query. `->data` is an array of entities; `->localizeTimestamps(?string $tz = null)` converts timestamps from UTC (default: operator's timezone) |

| Topic | Rule |
|---|---|
| Operators | `=` `!=` `<>` `<` `>` `<=` `>=` `LIKE` `NOT LIKE` `ILIKE` `NOT ILIKE` `~` `!~` `!~*` |
| Lists | `IN` / `NOT IN` take an array; `BETWEEN` / `NOT BETWEEN` take a two-item array |
| No value | `IS [NOT] NULL`, `IS [NOT] TRUE`, `IS [NOT] FALSE`, `IS [NOT] UNKNOWN`, `NOT` |
| Shorthand | Value `'null'`, `'true'` or `'false'` with `=` / `!=` becomes `IS [NOT] …` |
| Types | The column is cast by the PHP type of the value: int → `::int`, string → `::text`, array → none. Pass numbers as numbers: `'42'` compares as text |
| IP | `ip_ip` as text includes the mask: use `'203.0.113.7/32'`, `LIKE '203.0.113.%'` or `IN ['203.0.113.7']` |
| Typos | An unknown column or operator is ignored without an error |

### Columns

Aliases below, or real names such as `event_account.score`.

| Builder | Columns |
|---|---|
| `users` | `user_{id, userid, fullname, firstname, middlename, lastname, score, score_details, reviewed, fraud, score_recalculate, is_important, last_ip, total_visit, total_country, total_ip, total_device, total_shared_ip, total_shared_phone, lastseen, created, updated, score_updated_at, latest_decision, added_to_review}`, `email_{id, email, fraud_detected, checked, lastseen, created}`, `phone_{id, phone_number, shared, fraud_detected, invalid, checked, lastseen, created, updated}` |
| `ips` | `ip_{id, ip, cidr, data_center, tor, vpn, starlink, blocklist, relay, checked, shared, fraud_detected, total_visit, lastseen, created, updated}`, `isp_{id, asn, name, description, total_ip, total_visit, total_account, lastseen, created, updated}`, `country_{id, name, iso, data_id, total_visit, total_ip, total_account, lastseen, created, updated}` |
| `devices` | `device_{id, account_id, lang, total_visit, lastseen, created, updated}`, `user_agent_{id, device, browser_name, browser_version, os_name, os_version, user_agent, modified, checked, created}` |
| `sessions` | `session_{id, account_id, total_visit, total_device, total_ip, total_country, lastseen, created, updated}` |
| `countries` | `country_{id, name, iso, data_id, total_visit, total_ip, total_account, lastseen, created, updated}` |
| `referers` | `referer_{id, referer, lastseen, created}` |

### Entities

| Name | Return | Summary |
|---|---|---|
| `User->userid` | string | Your product's user identifier |
| `User->score` / `->scoreDetails` | ?int / ?array | Score and matched rules (`[['uid' => 'D02', 'score' => 0], …]`) |
| `User->firstname` / `->lastname` | ?string | Name |
| `User->totalIp` / `->totalDevice` | ?int | Number of IPs / devices |
| `User->fraud` | ?bool | Blacklisted (`true`), whitelisted (`false`), not set (`null`) |
| `User->lastseen` | string | Last activity (UTC) |
| `User->email->email` / `->phone->phoneNumber` | string | Latest email / phone (`null` when the user has none) |
| `User->setBlacklist()` / `setWhitelist()` | void | Set `fraud` to `true` / `false` |
| `Ip->ip` | string | IP address |
| `Ip->vpn` / `->tor` / `->dataCenter` | ?bool | Network flags |
| `Ip->totalVisit` / `->lastseen` | ?int / string | Visits / last seen (UTC) |
| `Ip->isp->name` / `->isp->asn` | ?string / int | ISP |
| `Ip->country->iso` / `->country->name` | string | Country |
| `Device->userId` / `->lang` / `->browserName` / `->osName` / `->device` | int / ?string | Device of a user |
| `Session->userId` / `->totalVisit` | int / ?int | Session of a user |
| `Country->iso` / `->name` / `->totalVisit` | string / string / ?int | Country totals |
| `Referer->referer` | ?string | Referrer URL |

## $user, $ip

| Name | Return | Summary |
|---|---|---|
| `$user->getById(int $id, ?int $key = null)` | ?`Entities\User` | One tracked user of the current key; `null` if not found |
| `$ip->getById(int $id, ?int $key = null)` | ?`Entities\Ip` | One IP address of the current key; `null` if not found |

## $rules, $rule

| Name | Return | Summary |
|---|---|---|
| `$rules->getAll(?int $key = null)` | ?`Entities\Rules` | All rules with the key's values, in `->rules` (`uid`, `name`, `value`; `value` is the key's score for the rule, set by the signup preset and the Rules engine) |
| `$rules->getByUserId(int $userId, ?int $key = null)` | ?`Entities\Rules` | Rules that matched one user, in `->rules` |
| `$rule->getById(string $uid, ?int $key = null)` | `Entities\Rule` | One rule by uid (see Known issues) |

## $entities

| Name | Return | Summary |
|---|---|---|
| `$entities->{name}` | proxy | Calls static methods of an entity class, e.g. `$entities->user->getById($id, $keyId)`. `->class` gives the class name. Names: `apiKey`, `country`, `countries`, `device`, `devices`, `email`, `emails`, `emptyEmail`, `event`, `events`, `httpRequest`, `httpResponse`, `ip`, `ips`, `isp`, `isps`, `logbook`, `operator`, `payload`, `payloads`, `phone`, `phones`, `emptyPhone`, `query`, `queries`, `emptyQuery`, `referer`, `referers`, `emptyReferer`, `rules`, `rule`, `session`, `sessions`, `user`, `users` |

`Entities\` = `\Tirreno\Entities\`.

## $db, $storage, $router, $log, $constants

| Name | Return | Summary |
|---|---|---|
| `$db->initConnection()` | bool | Connect using `DATABASE_URL`; `true` when connected |
| `$storage->get(string $key)` | mixed | Read from the global store (config values, request data) |
| `$storage->set(string $key, mixed $value)` / `remove(string $key)` | mixed | Write / delete in the global store |
| `tirreno('router')` | `\Base` | Fat-Free Framework instance |
| `$log->debug(string $format, mixed ...$args)` | void | Debug message, written only in debug mode |
| `$log->info(string $format, mixed ...$args)` | void | Info message to the log file (`LOG_FILE`) |
| `$log->warning(...)` / `error(...)` | void | Warning / error, also printed to stderr |
| `$constants->{NAME}` | mixed | Constant, e.g. `GUEST_OPERATOR_ID` (37), `UNAUTHORIZED_USERID` (`N/A`), `PRIMARY_RULES_SET_ID` (1) |

## $utils

| Name | Return | Summary |
|---|---|---|
| `$utils->nowUtc()` | string | Current UTC time, `Y-m-d H:i:s` |
| `$utils->timezones->localizeForActiveOperator(string $utcTime)` | string | Convert a UTC time to the operator's timezone, e.g. `localizeForActiveOperator($utils->nowUtc())` |
| `$utils->conversion->intVal(mixed $value, ?int $default = null)` | ?int | Parse an int, else `$default` |
| `$utils->errorCodes->{NAME}` | int\|string | Error code, e.g. `CSRF_ATTACK_DETECTED` (601) |
| `$utils->{name}` | proxy | Static methods of a utility class: `access`, `apiKeys`, `responseFormats`, `constants`, `conversion`, `cron`, `database`, `dateRange`, `dictManager`, `elapsedDate`, `enrichment`, `errorCodes`, `errorHandler`, `logger`, `mailer`, `network`, `operatorAccess`, `render`, `router`, `routes`, `rules`, `sort`, `systemMessages`, `timezones`, `updates`, `validators`, `variables`, `versionControl`, `httpClient` |

## $assets

| Name | Return | Summary |
|---|---|---|
| `$assets->aiBotList->getList()` | array | AI/LLM bot names (rule D13 "Device is AI bot") |
| `$assets->asnList->getList()` | array | ASN list |
| `$assets->emailList->getList()` | array | Suspicious email words (`spam`, `test`, …) |
| `$assets->userAgentList->getList()` | array | Suspicious user-agent fragments |
| `$assets->urlList->getList()` | array | Suspicious URL patterns (`%00`, `%20AND%20`, …) |
| `$assets->fileExtensionsList->getList()` | array | File extensions by category |
| `$assets->pages->getMenuPages()` | array | Custom pages shown in the menu (`route`, `title`) |

## $models, $grids, $charts, $controllers, $pages

Internal layers of the built-in console.

| Name | Return | Summary |
|---|---|---|
| `$models->{name}` | `\Tirreno\Models\…` | SQL data-access classes: `apiKeyCoOwner`, `apiKeys`, `blacklistItems`, `country`, `cursor`, `dashboard`, `device`, `domain`, `email`, `event`, `eventType`, `events`, `fieldAudit`, `fieldAuditTrail`, `forgotPassword`, `ip`, `isp`, `log`, `logbook`, `manualCheck`, `map`, `message`, `notification`, `operator`, `operatorsRoles`, `operatorsRules`, `pages`, `pagesPermissions`, `payload`, `permissions`, `phone`, `queue`, `resource`, `retentionPolicies`, `reviewQueue`, `roles`, `rolesPermissions`, `rules`, `session`, `sessionStat`, `updates`, `user`, `users`, `userAgent`, `userScore`, `userStat` |
| `$grids->{name}` | `\Tirreno\Models\Grid\…\Grid` | Table data: `blacklist`, `countries`, `devices`, `domains`, `emails`, `events`, `fieldAuditTrail`, `fieldAudits`, `ips`, `isps`, `logbook`, `phones`, `resources`, `reviewQueue`, `rules`, `users`, `userAgents` |
| `$charts->{name}` | `\Tirreno\Models\Chart\…` | Chart data: `blacklist`, `domains`, `emails`, `events`, `fields`, `ips`, `isps`, `logbook`, `phones`, `resources`, `reviewQueue`, `users`, `userAgents`, `watchlist`, `country`, `domain`, `field`, `ip`, `isp`, `resource`, `user`, `userAgent`, `userStats` |
| `$controllers->{name}` | `\Tirreno\Controllers\Services\…` | Section logic: `api`, `blacklist`, `context`, `countries`, `country`, `devices`, `domain`, `domains`, `emails`, `enrichment`, `events`, `field`, `fields`, `dashboard`, `ip`, `ips`, `isp`, `isps`, `logbook`, `manualCheck`, `phones`, `resource`, `resources`, `reviewQueue`, `rules`, `settings`, `user`, `users`, `userAgent`, `userAgents`, `main` |
| `$pages->{name}` | `\Tirreno\Controllers\Pages\…` | Console pages: `api`, `blacklist`, `countries`, `country`, `devices`, `domain`, `domains`, `emails`, `events`, `field`, `fields`, `dashboard`, `ip`, `ips`, `isp`, `isps`, `logbook`, `manualCheck`, `phones`, `resource`, `resources`, `reviewQueue`, `rules`, `settings`, `user`, `users`, `userAgent`, `userAgents`, `watchlist`, `main`, `error`, `logout` |

## How to use

1. Create two files: `assets/pages/<name>.php` for the logic and `assets/pages/views/<name>.html` for the template. 
2. The page opens at `/<name>` and appears in the console's left menu under the text passed to `$page->setTitle('…')` (write it as a plain string).
3. In the PHP file, call a service with `tirreno('name')`. These are already defined: `$page`, `$request`, `$response`, `$session`, `$sysop`, `$utils`, `$helpers`, `$constants`, `$db`, `$log`, `$user`, `$ip`.
4. Start with the `$response` guards: file-based pages also run for guests.
5. Pass data to the template with `$page->addParams([...])`. In the template, print with `{{ @NAME }}`, read array items with `{{ @row['key'] }}` and loop with `<repeat>`. Arrays and strings are HTML-escaped, so convert entities to arrays first.
6. An AJAX request (`X-Requested-With: XMLHttpRequest`) to `/<name>` returns the template variables as JSON.
7. Files named `*.example.php` are not listed or served. To enable a bundled example, copy `x.example.php` to `x.php` and `views/x.example.html` to `views/x.html`.

`assets/pages/vpn-ips.php`

```php
<?php
$page->setTitle('VPN IPs');
$response->redirectNotLoggedIn('/login');
$response->redirectImproperRole(['operator'], [], '/login');
$ips = tirreno('queries')->ips->where('ip_vpn', 'IS TRUE')->orderBy('ip_lastseen', 'DESC')->limit(50)->get()->data;
$page->addParams(['ROWS' => array_map(fn($ip) => ['ip' => $ip->ip, 'isp' => $ip->isp->name], $ips)]);
```

`assets/pages/views/vpn-ips.html`

```html
<ul><repeat group="{{ @ROWS }}" value="{{ @row }}"><li>{{ @row['ip'] }} | {{ @row['isp'] }}</li></repeat></ul>
```

## Known issues

| Name | Issue |
|---|---|
| `$queries->users`, `$user->getById()` | `TypeError` on accounts that are not scored yet (new accounts until the risk-score cron runs). Add `->where('user_score_updated_at', 'IS NOT NULL')` |
| `$rule->getById()` | Returns the `value` of key 1, not the current key (use `$rules->getAll()`); an unknown uid throws `TypeError` |
| `->find()` | `>=`, `<=`, `!=`, `<>` produce wrong conditions; `<` and `>` compare as text |
| `tirreno('users')`, `tirreno('ips')` | `TypeError` for a guest (use `$queries->users` / `$queries->ips`) |
| `$utils->nowForCurrentOperator()` | Returns UTC time; use `$utils->timezones->localizeForActiveOperator($utils->nowUtc())` |
| Operator `*~` | SQL error; use `ILIKE` or `!~*` |

## Resources

| Resource | URL |
|----------|-----|
| Live Demo | [play.tirreno.com](https://play.tirreno.com) (admin/tirreno) |
| Resource center | [tirreno.com/bat](https://www.tirreno.com/bat/) |
| Developers Guide | [github.com/tirrenotechnologies/DEVELOPMENT.md](https://github.com/tirrenotechnologies/DEVELOPMENT.md) |
| Administrator guide | [github.com/tirrenotechnologies/ADMIN.md](https://github.com/tirrenotechnologies/ADMIN.md) |
| Operator guide | [github.com/tirrenotechnologies/OPERATOR.md](https://github.com/tirrenotechnologies/OPERATOR.md) |
| API reference | [github.com/tirrenotechnologies/API.md](https://github.com/tirrenotechnologies/API.md) |
| GitHub | [github.com/tirrenotechnologies/tirreno](https://github.com/tirrenotechnologies/tirreno) |
| GitLab Mirror | [gitlab.com/tirreno/tirreno](https://gitlab.com/tirreno/tirreno) |
| Docker Hub | [hub.docker.com/r/tirreno/tirreno](https://hub.docker.com/r/tirreno/tirreno) |
| Packagist | [packagist.org/packages/tirreno/tirreno](https://packagist.org/packages/tirreno/tirreno) |
| PHP Tracker | [github.com/tirrenotechnologies/tirreno-php-tracker](https://github.com/tirrenotechnologies/tirreno-php-tracker) |
| Python Tracker | [github.com/tirrenotechnologies/tirreno-python-tracker](https://github.com/tirrenotechnologies/tirreno-python-tracker) |
| Node.js Tracker | [github.com/tirrenotechnologies/tirreno-nodejs-tracker](https://github.com/tirrenotechnologies/tirreno-nodejs-tracker) |
| WordPress Tracker | [github.com/tirrenotechnologies/tirreno-wordpress-tracker](https://github.com/tirrenotechnologies/tirreno-wordpress-tracker) |
| Community Chat | [chat.tirreno.com](https://chat.tirreno.com) |

---

## Found a mistake?

If you have found a mistake in the documentation, no matter how large or small, please let us know by [creating a new issue](https://github.com/tirrenotechnologies/tirreno/issues) in the tirreno repository.

---

## License

tirreno and this documentation are licensed under the **GNU Affero General Public License v3 (AGPL-3.0)**.

The name "tirreno" is a registered trademark of tirreno technologies sàrl.

---

*tirreno Copyright (C) 2026 tirreno technologies sàrl, Vaud, Switzerland.*
