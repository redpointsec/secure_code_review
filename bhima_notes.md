# BHIMA

We assessed commit `47ae140a78b23d1d4d9350c7e8b6caeae50d8335 `

---

# Notes for you/your team

## Behavior

* What does it do? (business purpose)
  >Bhima is a free, open source accounting and hospital information management system (HIMS) tailored for rural hospitals in Africa. We are an international team based in the Democratic Republic of the Congo.
* Who does it do this for? (internal / external customer base)
  >External Customers
* What kind of information will it hold?
>PII, Accounting Data
* What are the different types of roles?
> Admin, User Role, Custom Roles
[bhima/roles.sql at 47ae140a78b23d1d4d9350c7e8b6caeae50d8335 · IMA-WorldHealth/bhima · GitHub](https://github.com/IMA-WorldHealth/bhima/blob/47ae140a78b23d1d4d9350c7e8b6caeae50d8335/server/models/procedures/roles.sql#L9-L10

```sql
INSERT INTO role(uuid, label)
    VALUES(roleUUID, 'Administrateur');
```
* What aspects concern your client/customer/staff the most?
*

## Tech Stack
Language|files|blank|comment|code
:-------|-------:|-------:|-------:|-------:
JavaScript|1374|21642|22193|94244
HTML|633|3325|884|27528
SQL|40|1760|1623|9164
Handlebars|98|835|305|9141
JSON|162|8|0|7766
Markdown|106|1274|0|3221
CSS|6|317|211|1381
LESS|1|83|49|336
SVG|3|0|3|279
Bourne Shell|10|112|51|228
YAML|5|24|1|93
Sass|1|2|0|59
TOML|1|1|0|5
--------|--------|--------|--------|--------
SUM:|2440|29383|25320|153445
* Framework & Language - Nodejs/Express, Angular, Helmet, handlbars
```noded
npm audit
found 5 vulnerabilities (2 low, 3 moderate) in 17129 scanned packages
```
[Node Dependencies](#Dependencies)

* Datastore - MySQL
* Documentation - [Home · IMA-WorldHealth/bhima Wiki · GitHub](https://github.com/IMA-WorldHealth/bhima/wiki)

## Brainstorming / Risks

* Patient Date PII 
* Accounting Data
>BHIMA aims to provide a flexible and robust accounting and managerial solution for rural hospitals. This includes, but is not limited to, basic income/expense reporting, budgeting, patient and organisational billing, depreciation, inventory and pricing, and purchasing.

## Checklist of things to review 

### Risks
- [ ] how patient data is stored/accesses (CRUD)
- [ ] Signup, Self Registration vs Admin registration
- [ ] database calls, auth on calls
- [ ] file uploads
- [ ] Xss, Data Exposure, Misconfig w/Angular
- [ ] Secrets in Code

### Configuration
- [ ] [bhima/express.js at 47ae140a78b23d1d4d9350c7e8b6caeae50d8335 · IMA-WorldHealth/bhima · GitHub](https://github.com/IMA-WorldHealth/bhima/blob/47ae140a78b23d1d4d9350c7e8b6caeae50d8335/server/config/express.js#L1-L121)
## Mapping / Routes
```sh
cat server/config/routes.js | grep "app\." | awk '{print "- [ ] "$0 }' | wc -l         
     607
  ```
[bhima/express.js at 47ae140a78b23d1d4d9350c7e8b6caeae50d8335 · IMA-WorldHealth/bhima · GitHub](https://github.com/IMA-WorldHealth/bhima/blob/47ae140a78b23d1d4d9350c7e8b6caeae50d8335/server/config/express.js#L24-L32)

```javascript
const publicRoutes = [
  '/auth/login',
  '/languages',
  '/projects',
  '/projects/',
  '/auth/logout',
  '/install',
  '/currencies',
];
```
##### Authentication/Authorization
- [X]   app.post('/auth/login', auth.login);
- [ ]   app.put('/users/:id/password', users.password);
- [ ]   app.put('/roles/:uuid', rolesCtrl.update);
- [ ]   app.post('/roles', rolesCtrl.create);
- [ ]   app.post('/employees', employees.create);
##### uploads/imports
- [ ]   app.post('/accounts/import', upload.middleware('csv', 'file'), accounts.importing.importAccounts);
- [ ]   app.post('/inventory/import/', upload.middleware('csv', 'file'), inventory.importing.importInventories);
- [ ]   app.post('/patients/:uuid/documents', upload.middleware('docs', 'documents'), patients.documents.create);
- [ ]   app.post('/patients/:uuid/pictures', upload.middleware('pics', 'pictures'), patients.pictures.set);
- [ ]   app.post('/prices/item/import', upload.middleware('csv', 'file'), priceList.importItem);
- [ ]   app.post('/enterprises/:id/logo', upload.middleware('pics', 'logo'), enterprises.uploadLogo);
- [ ]   app.post('/stock/import', upload.middleware('csv', 'file'), upload.hasFilesToUpload, stock.importing.importStock);
- [ ]   app.post('/fill_form/:uuid/:key/image', upload.middleware('pics', 'image'), fillFormsData.uploadImage);

##### admin behavior
- [ ]   app.post('/employee_config/:id/setting', employeeConfig.createConfig);
- [ ]   app.put('/crons/:id', cron.update);
- [ ]   app.put('/account_config/:id', accountConfig.update);
- [ ]   app.put('/weekend_config/:id', weekendConfig.update);
- [ ]   app.put('/employee_config/:id', employeeConfig.update);
- [ ]   app.put('/employees/:uuid', employees.update);

##### Financial Functions
- [ ]   app.put('/cashboxes/:id', cashboxes.update);
- [ ]   app.put('/cashboxes/:id/currencies/:currencyId', cashboxes.currencies.update);
- [ ]   app.put('/prices/:uuid', priceList.update);
- [ ]   app.put('/cash/:uuid', cash.update);
- [ ]   app.put('/enterprises/:id', enterprises.update);
- [ ]   app.put('/employees/:uuid', employees.update);
- [ ]   app.put('/invoicing_fees/:id', invoicingFees.update);
- [ ]   app.put('/discounts/:id', discounts.update);
- [ ]   app.put('/suppliers/:uuid', suppliers.update);
- [ ]   app.put('/purchases/:uuid', purchases.update);
- [ ]   app.put('/payroll_config/:id', payrollConfig.update);
- [X]   app.put('/accounts/:id', accounts.update);
   No Auth checks before updates
   [bhima/index.js at 47ae140a78b23d1d4d9350c7e8b6caeae50d8335 · IMA-WorldHealth/bhima · GitHub](https://github.com/IMA-WorldHealth/bhima/blob/47ae140a78b23d1d4d9350c7e8b6caeae50d8335/server/controllers/finance/accounts/index.js#L65-L89)

```javascript
/**
 * @method update
 *
 * @description
 * Updates an account in the database.
 *
 * PUT /accounts/:id
 */
function update(req, res, next) {
  const { id } = req.params;
  const data = req.body;
  const sql = 'UPDATE account SET ? WHERE id = ?';

  delete data.id;

  utility.lookupAccount(id)
    .then(() => db.exec(sql, [data, id]))
    .then(() => utility.lookupAccount(id))
    .then((account) => {
      res.status(200).json(account);
    })
    .catch(next)
    .done();
}

```
- [ ]   app.put('/services/:id', services.update);
- [ ]   app.put('/subsidies/:id', subsidies.update);
- [ ]   app.put('/fiscal/:id', fiscal.update);
#### User Data
- [ ]   app.put('/patients/:uuid', patients.update);

### Authorization
`bhima/server/controllers/auth.js`
> * This controller is responsible for managing user authentication and
>authorization to the entire application stack.

[bhima/auth.js at 47ae140a78b23d1d4d9350c7e8b6caeae50d8335 · IMA-WorldHealth/bhima · GitHub](https://github.com/IMA-WorldHealth/bhima/blob/47ae140a78b23d1d4d9350c7e8b6caeae50d8335/server/controllers/auth.js#L72-L79)

```javascript
// a role should be assigned to the user
  // each role has some units(paths or urls) that the user is allowed to access(permissions)
  const sqlPermission = `
    SELECT  user.id
    FROM  user_role
      JOIN user ON user.id =  user_role.user_id
    WHERE user.username = ? AND user.password = PASSWORD(?)
  `;
```
- [ ] Finace Account Mangement `bhima/server/controllers/finance/accounts/index.js`


### Authentication
* User Enum 
`app.get('/users/:username/exists', users.exists);`
!!! caution User Enum and Data leakage
[bhima/index.js at 47ae140a78b23d1d4d9350c7e8b6caeae50d8335 · IMA-WorldHealth/bhima · GitHub](https://github.com/IMA-WorldHealth/bhima/blob/47ae140a78b23d1d4d9350c7e8b6caeae50d8335/server/controllers/admin/users/index.js#L117-L146)

```javascript
/**
 * @function detail
 *
 * @description
 * This endpoint will return a single JSON object containing the full user row
 * for the user with matching ID.  If no matching user exists, it will return a
 * 404 error.
 *
 * For consistency with the CREATE method, this route also returns a user's project
 * permissions.
 *
 * GET /users/:id
 */
function detail(req, res, next) {
  lookupUser(req.params.id)
    .then((data) => {
      res.status(200).json(data);
    })
    .catch(next);
}

function exists(req, res, next) {
  const sql = 'SELECT count(id) as nbr FROM user WHERE username=?';

  db.one(sql, req.params.username)
    .then((data) => {
      res.send(data.nbr !== 0);
    })
    .catch(next);
}
```

[bhima/server/models/schema.sql](https://github.com/IMA-WorldHealth/bhima/blob/47ae140a78b23d1d4d9350c7e8b6caeae50d8335/server/models/schema.sql#L1757-L1769)

```sql
CREATE TABLE `user` (
  `id`            SMALLINT(5) UNSIGNED NOT NULL AUTO_INCREMENT,
  `username`      VARCHAR(80) NOT NULL,
  `password`      VARCHAR(100) NOT NULL,
  `display_name`  TEXT NOT NULL,
  `email`         VARCHAR(100) DEFAULT NULL,
  `active`        TINYINT(4) NOT NULL DEFAULT 0,
  `deactivated`   TINYINT(1) NOT NULL DEFAULT 0,
  `last_login`    TIMESTAMP NULL ON UPDATE CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`),
  UNIQUE KEY `user_1` (`username`)
) ENGINE=InnoDB DEFAULT CHARACTER SET = utf8mb4 DEFAULT COLLATE = utf8mb4_unicode_ci;

```
!!!
* Login page give error messages, check for enumeration
` app.post('/auth/login', auth.login);`

!!! caution Username Enumeration
[bhima/auth.js at 47ae140a78b23d1d4d9350c7e8b6caeae50d8335 · IMA-WorldHealth/bhima · GitHub](https://github.com/IMA-WorldHealth/bhima/blob/47ae140a78b23d1d4d9350c7e8b6caeae50d8335/server/controllers/auth.js#L91-L103)

```javascript
if (hasAuthorization) {
    if (Boolean(user[0].deactivated)) {
      throw new Unauthorized('The user is not activated, contact the administrator', 'FORM.ERRORS.LOCKED_USER');
    }

    if (isMissingPermissions) {
      throw new Unauthorized('No permissions in the database.', 'ERRORS.NO_PERMISSIONS');
    }
  } else if (isUnrecognizedUser) {
    throw new Unauthorized('Bad username and password combination.');
  } else {
    throw new Unauthorized('No permissions for that project.', 'ERRORS.NO_PROJECT');
  }
```
!!!
* Password Change
`app.put('/users/:id/password', users.password);`
!!! caution password reset
[bhima/index.js at 47ae140a78b23d1d4d9350c7e8b6caeae50d8335 · IMA-WorldHealth/bhima · GitHub](https://github.com/IMA-WorldHealth/bhima/blob/47ae140a78b23d1d4d9350c7e8b6caeae50d8335/server/controllers/admin/users/index.js#L255-L275)

```javascript
/**
 * @function password
 *
 * @description
 * PUT /users/:id/password
 *
 * This endpoint updates a user's password with ID :id.  If the user is not
 * found, the server sends back a 404 error.
 */
function password(req, res, next) {
  // TODO -- strict check to see if the user is either signed in or has
  // sudo permissions.
  const sql = `UPDATE user SET password = PASSWORD(?) WHERE id = ?;`;

  db.exec(sql, [req.body.password, req.params.id])
    .then(() => lookupUser(req.params.id))
    .then((data) => {
      res.status(200).json(data);
    })
    .catch(next);
}
```
!!!
- [ ] Forgot password


## Mapping / Authorization Decorators

**didn't see any yet**

## Auditing/Logging
Debug Package
`bhima/node_modules/@types/debug/index.d.ts`

!!! note Logging IP GDPR?
[bhima/express.js at 47ae140a78b23d1d4d9350c7e8b6caeae50d8335 · IMA-WorldHealth/bhima · GitHub](https://github.com/IMA-WorldHealth/bhima/blob/47ae140a78b23d1d4d9350c7e8b6caeae50d8335/server/config/express.js#L107-L115)

```javascript
app.use((req, res, next) => {
    if (_.isUndefined(req.session.user) && !within(req.path, publicRoutes)) {
      debug(`Rejecting unauthorized access to ${req.path} from ${req.ip}`);
      next(new Unauthorized('You are not logged into the system.'));
    } else {
      next();
    }
  });
};
```
!!!

!!! caution Logging AccountID and Balance
[bhima/index.js at 47ae140a78b23d1d4d9350c7e8b6caeae50d8335 · IMA-WorldHealth/bhima · GitHub](https://github.com/IMA-WorldHealth/bhima/blob/47ae140a78b23d1d4d9350c7e8b6caeae50d8335/server/controllers/finance/accounts/index.js#L318-L320)

```javascript
.then(balances => {
      debug('#getOpeningBalanceForPeriod() computed %j balances for account id %s.', balances, accountId);
      res.status(200).json(balances);
```
!!!

## Mapping / Files

- [ ] /path/to/some/important/file.sh







### Injection
* Cmd Injection but not really
```javascript
/**
 * @method execp
 *
 * @description
 * This method promisifies the child process exec() function.  It is used in
 * lib/backup.js, but will likely be handy in other places as well.
 */
function execp(cmd) {
  debug(`#execp(): ${cmd}`);
  const deferred = q.defer();
  const child = exec(cmd);
  child.addListener('error', deferred.reject);
  child.addListener('exit', deferred.resolve);
  return deferred.promise;
}

```
!!!

### Cryptography
!!! caution Session Secret

[bhima/.env.development at 47ae140a78b23d1d4d9350c7e8b6caeae50d8335 · IMA-WorldHealth/bhima · GitHub](https://github.com/IMA-WorldHealth/bhima/blob/47ae140a78b23d1d4d9350c7e8b6caeae50d8335/.env.development#L9-L11)

```plaintext
# session variables
SESS_SECRET='XopEn BlowFISH'
```
!!!

!!! caution mysql PASSWORD used 
[bhima/index.js at 47ae140a78b23d1d4d9350c7e8b6caeae50d8335 · IMA-WorldHealth/bhima · GitHub](https://github.com/IMA-WorldHealth/bhima/blob/47ae140a78b23d1d4d9350c7e8b6caeae50d8335/server/controllers/admin/users/index.js#L167-L170)

```javascript
let sql = `
    INSERT INTO user (username, password, email, display_name) VALUES
    (?, PASSWORD(?), ?, ?);
  `;
```

[Mysql Password](https://mysqlserverteam.com/protecting-mysql-passwords-with-the-sha256_password-plugin/)
!!!

## Config
!!! note Superuser defaults
```sql
-- create a superuser
INSERT INTO user (id, username, password, display_name, email, deactivated) VALUE
  (1, 'superuser', PASSWORD('superuser'), 'Adminstrator', 'developper@imaworldhealth.org', 0);
  ```
!!!
## Dependencies

```json
 "@bower_components/angular-ui-router": "angular-ui/angular-ui-router-bower#1.0.0-rc.1",
    "@types/angular": "^1.6.53",
    "accounting-js": "^1.1.1",
    "angular": "^1.7.8",
    "angular-animate": "^1.7.8",
    "angular-dynamic-locale": "^0.1.37",
    "angular-growl-notifications": "^2.6.0",
    "angular-messages": "^1.7.8",
    "angular-moment": "^1.3.0",
    "angular-sanitize": "^1.7.8",
    "angular-touch": "^1.7.8",
    "angular-translate": "^2.18.1",
    "angular-translate-loader-static-files": "^2.18.1",
    "angular-translate-loader-url": "^2.18.1",
    "angular-ui-bootstrap": "^2.5.6",
    "angular-ui-grid": "^4.8.0",
    "angular-ui-router": "angular-ui/angular-ui-router-bower#1.0.0-rc.1",
    "body-parser": "^1.18.3",
    "bootstrap": "^3.3.0",
    "chai-spies": "^1.0.0",
    "chai-spies-next": "^0.9.3",
    "connect-redis": "^4.0.3",
    "cron": "^1.7.1",
    "cropper": "^4.0.0",
    "cross-env": "^6.0.0",
    "csvtojson": "^2.0.8",
    "debug": "^4.1.1",
    "dotenv": "^8.0.0",
    "excel4node": "^1.7.0",
    "express": "^4.16.4",
    "express-handlebars": "^3.0.0",
    "express-session": "^1.15.6",
    "font-awesome": "^4.7.0",
    "helmet": "^3.21.1",
    "ioredis": "^4.14.1",
    "jquery": "^3.4.0",
    "jsbarcode": "^3.11.0",
    "json-2-csv": "^3.2.0",
    "juice": "^5.1.0",
    "lodash": "^4.17.11",
    "lzma-native": "^4.0.3",
    "mailgun-js": "^0.22.0",
    "mkdirp": "^0.5.1",
    "moment": "^2.24.0",
    "morgan": "^1.9.1",
    "multer": "^1.4.1",
    "muze": "^1.2.1",
    "mysql": "^2.16.0",
    "mz": "^2.7.0",
    "ng-file-upload": "^12.2.13",
    "ngstorage": "^0.3.11",
    "q": "~1.5.1",
    "stream-to-promise": "^2.2.0",
    "tempy": "^0.3.0",
    "typeface-open-sans": "^0.0.75",
    "ui-select": "0.17.1",
    "use-strict": "^1.0.1",
    "uuid": "^3.3.2",
    "uuid-parse": "^1.0.0",
    "webcam": "^3.2.1",
    "winston": "^3.2.1",
    "wkhtmltopdf": "^0.3.4"
  },
  ```
## All Routes
- [ ]   app.get('/languages', languages.list);
- [ ]   app.get('/projects', projects.list);
- [ ]   app.get('/units', units.list);
- [ ]   app.post('/auth/login', auth.login);
- [ ]   app.get('/auth/logout', auth.logout);
- [ ]   app.post('/auth/reload', auth.reload);
- [ ]   app.get('/system/information', system.info);
- [ ]   app.get('/patients/stats', stats.patients);
- [ ]   app.get('/invoices/stats', stats.invoices);
- [ ]   app.get('/exchange', exchange.list);
- [ ]   app.post('/exchange', exchange.create);
- [ ]   app.put('/exchange/:id', exchange.update);
- [ ]   app.delete('/exchange/:id', exchange.delete);
- [ ]   app.get('/locations/villages', locations.villages);
- [ ]   app.get('/locations/sectors', locations.sectors);
- [ ]   app.get('/locations/provinces', locations.provinces);
- [ ]   app.get('/locations/countries', locations.countries);
- [ ]   app.post('/locations/countries', locations.create.country);
- [ ]   app.post('/locations/provinces', locations.create.province);
- [ ]   app.post('/locations/sectors', locations.create.sector);
- [ ]   app.post('/locations/villages', locations.create.village);
- [ ]   app.get('/locations/detail/:uuid', locations.detail);
- [ ]   app.get('/locations/detail/', locations.list);
- [ ]   app.put('/locations/villages/:uuid', locations.update.village);
- [ ]   app.put('/locations/sectors/:uuid', locations.update.sector);
- [ ]   app.put('/locations/provinces/:uuid', locations.update.province);
- [ ]   app.put('/locations/countries/:uuid', locations.update.country);
- [ ]   app.delete('/locations/countries/:uuid', locations.delete.country);
- [ ]   app.delete('/locations/provinces/:uuid', locations.delete.province);
- [ ]   app.delete('/locations/sectors/:uuid', locations.delete.sector);
- [ ]   app.delete('/locations/villages/:uuid', locations.delete.village);
- [ ]   app.post('/groups/:key/:id', groups.updateSubscriptions);
- [ ]   app.get('/accounts/types', accounts.types.list);
- [ ]   app.get('/accounts/types/:id', accounts.types.detail);
- [ ]   app.post('/accounts/types', accounts.types.create);
- [ ]   app.put('/accounts/types/:id', accounts.types.update);
- [ ]   app.delete('/accounts/types/:id', accounts.types.remove);
- [ ]   app.get('/accounts/categories', accounts.categories.list);
- [ ]   app.get('/accounts/categories/:id', accounts.categories.detail);
- [ ]   app.post('/accounts/categories', accounts.categories.create);
- [ ]   app.put('/accounts/categories/:id', accounts.categories.update);
- [ ]   app.delete('/accounts/categories/:id', accounts.categories.remove);
- [ ]   app.get('/accounts/references/values/:periodId', accounts.references.getAllValues);
- [ ]   app.get('/accounts/references/values/:periodId/:abbr/:isAmoDep?', accounts.references.getValue);
- [ ]   app.get('/accounts/references', accounts.references.list);
- [ ]   app.get('/accounts/references/:id', accounts.references.detail);
- [ ]   app.post('/accounts/references', accounts.references.create);
- [ ]   app.put('/accounts/references/:id', accounts.references.update);
- [ ]   app.delete('/accounts/references/:id', accounts.references.remove);
- [ ]   app.get('/accounts/template', accounts.importing.downloadTemplate);
- [ ]   app.post('/accounts/import', upload.middleware('csv', 'file'), accounts.importing.importAccounts);
- [ ]   app.get('/accounts', accounts.list);
- [ ]   app.get('/accounts/:id', accounts.detail);
- [ ]   app.get('/accounts/:id/balance', accounts.getBalance);
- [ ]   app.get('/accounts/:id/balance/:fiscalYearId', accounts.getAnnualBalance);
- [ ]   app.get('/accounts/:id/openingBalance', accounts.getOpeningBalanceForPeriod);
- [ ]   app.post('/accounts', accounts.create);
- [ ]   app.put('/accounts/:id', accounts.update);
- [ ]   app.delete('/accounts/:id', accounts.remove);
- [ ]   app.post('/services', services.create);
- [ ]   app.get('/services', services.list);
- [ ]   app.get('/services/count', services.countServiceByProject);
- [ ]   app.get('/services/:id', services.detail);
- [ ]   app.put('/services/:id', services.update);
- [ ]   app.delete('/services/:id', services.remove);
- [ ]   app.get('/subsidies', subsidies.list);
- [ ]   app.get('/subsidies/:id', subsidies.detail);
- [ ]   app.post('/subsidies', subsidies.create);
- [ ]   app.put('/subsidies/:id', subsidies.update);
- [ ]   app.delete('/subsidies/:id', subsidies.remove);
- [ ]   app.get('/journal', journal.list);
- [ ]   app.get('/journal/count', journal.count);
- [ ]   app.post('/journal/trialbalance', trialBalance.runTrialBalance);
- [ ]   app.post('/journal/transactions', trialBalance.postToGeneralLedger);
- [ ]   app.get('/journal/:record_uuid', journal.getTransaction);
- [ ]   app.post('/journal/:record_uuid/edit', journal.editTransaction);
- [ ]   app.post('/journal/:uuid/reverse', journal.reverse);
- [ ]   app.post('/journal/:uuid/correct', voucherTools.correct);
- [ ]   app.put('/transactions/comments', transactions.commentTransactions);
- [ ]   app.get('/general_ledger', generalLedger.list);
- [ ]   app.get('/general_ledger/transactions', generalLedger.getTransactions);
- [ ]   app.get('/general_ledger/aggregates', generalLedger.getAggregates);
- [ ]   app.get('/transactions/:uuid/history', journal.getTransactionEditHistory);
- [ ]   app.delete('/transactions/:uuid', transactions.deleteRoute);
- [ ]   app.get('/fiscal', fiscal.list);
- [ ]   app.get('/fiscal/date', fiscal.getFiscalYearsByDate);
- [ ]   app.get('/fiscal/:id', fiscal.detail);
- [ ]   app.post('/fiscal', fiscal.create);
- [ ]   app.put('/fiscal/:id', fiscal.update);
- [ ]   app.delete('/fiscal/:id', fiscal.remove);
- [ ]   app.get('/fiscal/:id/balance/:period_number?', fiscal.getBalance);
- [ ]   app.get('/fiscal/:id/opening_balance', fiscal.getOpeningBalanceRoute);
- [ ]   app.post('/fiscal/:id/opening_balance', fiscal.setOpeningBalance);
- [ ]   app.put('/fiscal/:id/closing', fiscal.closing);
- [ ]   app.get('/fiscal/:id/closing_balance', fiscal.getClosingBalanceRoute);
- [ ]   app.get('/fiscal/:id/periods', fiscal.getPeriods);
- [ ]   app.get('/periods', period.list);
- [ ]   app.get('/periods/:id', period.details);
- [ ]   app.get('/tree', tree.generate);
- [ ]   app.get('/snis/healthZones', snis.healthZones);
- [ ]   app.get('/holiday_list/:pp/:employee_id', employees.listHolidays);
- [ ]   app.get('/getCheckHollyday/', employees.checkHoliday);
- [ ]   app.get('/getCheckOffday/', employees.checkOffday);
- [ ]   app.post('/inventory/metadata', inventory.createInventoryItems);
- [ ]   app.get('/inventory/metadata', inventory.getInventoryItems);
- [ ]   app.get('/inventory/metadata/:uuid', inventory.getInventoryItemsById);
- [ ]   app.put('/inventory/metadata/:uuid', inventory.updateInventoryItems);
- [ ]   app.delete('/inventory/metadata/:uuid', inventory.deleteInventory);
- [ ]   app.get('/inventory/log/:uuid', inventory.logs);
- [ ]   app.post('/inventory/groups', inventory.createInventoryGroups);
- [ ]   app.get('/inventory/groups', inventory.listInventoryGroups);
- [ ]   app.get('/inventory/groups/:uuid', inventory.detailsInventoryGroups);
- [ ]   app.get('/inventory/groups/:uuid/count', inventory.countInventoryGroups);
- [ ]   app.put('/inventory/groups/:uuid', inventory.updateInventoryGroups);
- [ ]   app.delete('/inventory/groups/:uuid', inventory.deleteInventoryGroups);
- [ ]   app.post('/inventory/types', inventory.createInventoryTypes);
- [ ]   app.get('/inventory/types', inventory.listInventoryTypes);
- [ ]   app.get('/inventory/types/:id', inventory.detailsInventoryTypes);
- [ ]   app.put('/inventory/types/:id', inventory.updateInventoryTypes);
- [ ]   app.delete('/inventory/types/:id', inventory.deleteInventoryTypes);
- [ ]   app.post('/inventory/units', inventory.createInventoryUnits);
- [ ]   app.get('/inventory/units', inventory.listInventoryUnits);
- [ ]   app.get('/inventory/units/:id', inventory.detailsInventoryUnits);
- [ ]   app.put('/inventory/units/:id', inventory.updateInventoryUnits);
- [ ]   app.delete('/inventory/units/:id', inventory.deleteInventoryUnits);
- [ ]   app.post('/inventory/import/', upload.middleware('csv', 'file'), inventory.importing.importInventories);
- [ ]   app.get('/inventory/import/template_file', inventory.importing.downloadTemplate);
- [ ]   app.get('/depots', depots.list);
- [ ]   app.get('/depots/:uuid', depots.detail);
- [ ]   app.put('/depots/:uuid', depots.update);
- [ ]   app.post('/depots', depots.create);
- [ ]   app.delete('/depots/:uuid', depots.remove);
- [ ]   app.get('/ledgers/general', gl.route);
- [ ]   app.get('/currencies', currencies.list);
- [ ]   app.get('/currencies/:id', currencies.detail);
- [ ]   app.get('/invoices', patientInvoice.read);
- [ ]   app.post('/invoices', patientInvoice.create);
- [ ]   app.get('/invoices/consumable/', patientInvoice.lookupConsumableInvoicePatient);
- [ ]   app.get('/invoices/:uuid', patientInvoice.detail);
- [ ]   app.get('/invoices/:uuid/balance', patientInvoice.balance);
- [ ]   app.get('/referenceLookup/:codeRef/:language', referenceLookup.getEntity);
- [ ]   app.get('/reports/payroll/employees', employeeReports.employeeRegistrations);
- [ ]   app.get('/reports/payroll/multipayroll', employeeReports.employeeMultiPayroll);
- [ ]   app.get('/reports/payroll/payslip', employeeReports.payslipGenerator);
- [ ]   app.get('/payroll_config', payrollConfig.list);
- [ ]   app.get('/payroll_config/paiementStatus', payrollConfig.paiementStatus);
- [ ]   app.get('/payroll_config/:id', payrollConfig.detail);
- [ ]   app.post('/payroll_config', payrollConfig.create);
- [ ]   app.put('/payroll_config/:id', payrollConfig.update);
- [ ]   app.delete('/payroll_config/:id', payrollConfig.delete);
- [ ]   app.post('/staffing_indices', staffingIndices.create);
- [ ]   app.get('/staffing_indices', staffingIndices.list);
- [ ]   app.get('/staffing_indices/:uuid', staffingIndices.detail);
- [ ]   app.get('/staffing_indices/export/report', staffingIndicesReport.document);
- [ ]   app.delete('/staffing_indices/:uuid', staffingIndices.remove);
- [ ]   app.put('/staffing_indices/:uuid', staffingIndices.update);
- [ ]   app.get('/staffing_function_indices/', staffingIndices.functionIndices.list);
- [ ]   app.get('/staffing_function_indices/:uuid', staffingIndices.functionIndices.detail);
- [ ]   app.post('/staffing_function_indices', staffingIndices.functionIndices.create);
- [ ]   app.put('/staffing_function_indices/:uuid', staffingIndices.functionIndices.update);
- [ ]   app.delete('/staffing_function_indices/:uuid', staffingIndices.functionIndices.delete);
- [ ]   app.get('/staffing_grade_indices/', staffingIndices.gradeIndices.list);
- [ ]   app.get('/staffing_grade_indices/:uuid', staffingIndices.gradeIndices.detail);
- [ ]   app.post('/staffing_grade_indices', staffingIndices.gradeIndices.create);
- [ ]   app.put('/staffing_grade_indices/:uuid', staffingIndices.gradeIndices.update);
- [ ]   app.delete('/staffing_grade_indices/:uuid', staffingIndices.gradeIndices.delete);
- [ ]   app.get('/reports/medical/patients', medicalReports.patientRegistrations);
- [ ]   app.get('/reports/medical/patients/:uuid', medicalReports.receipts.patients);
- [ ]   app.get('/reports/medical/patients/:uuid/visits', medicalReports.patientVisits);
- [ ]   app.get('/reports/inventory/purchases/:uuid', inventoryReports.receipts.purchases);
- [ ]   app.get('/reports/inventory/items', inventoryReports.reports.prices);
- [ ]   app.get('/reports/finance/invoices', financeReports.invoices.report);
- [ ]   app.get('/reports/finance/invoices/:uuid', financeReports.invoices.receipt);
- [ ]   app.get('/reports/finance/invoices/:uuid/creditNote', financeReports.invoices.creditNote);
- [ ]   app.get('/reports/finance/cash', financeReports.cash.report);
- [ ]   app.get('/reports/finance/cash/:uuid', financeReports.cash.receipt);
- [ ]   app.get('/reports/finance/debtors/aged', financeReports.debtors.aged);
- [ ]   app.get('/reports/finance/debtors/open', financeReports.debtors.open);
- [ ]   app.get('/reports/finance/vouchers', financeReports.vouchers.report);
- [ ]   app.get('/reports/finance/vouchers/:uuid', financeReports.vouchers.receipt);
- [ ]   app.get('/reports/finance/accounts/chart', financeReports.accounts.chart);
- [ ]   app.get('/reports/finance/cashflow/', financeReports.cashflow.report);
- [ ]   app.get('/reports/finance/cashflow/services', financeReports.cashflow.byService);
- [ ]   app.get('/reports/finance/financialPatient/:uuid', financeReports.patient);
- [ ]   app.get('/reports/finance/income_expense', financeReports.income_expense.document);
- [ ]   app.get('/reports/finance/unpaid-invoice-payments', unpaidInvoicePayments.document);
- [ ]   app.get('/reports/finance/income_expense_by_month', financeReports.income_expense_by_month.document);
- [ ]   app.get('/reports/finance/income_expense_by_year', financeReports.income_expense_by_year.document);
- [ ]   app.get('/reports/finance/cash_report', financeReports.cashReport.document);
- [ ]   app.get('/reports/finance/balance', financeReports.balance.document);
- [ ]   app.get('/reports/finance/monthly_balance', financeReports.monthlyBalance.document);
- [ ]   app.get('/reports/finance/account_report', financeReports.reportAccounts.document);
- [ ]   app.get('/reports/finance/account_report_multiple', financeReports.reportAccountsMultiple.document);
- [ ]   app.get('/reports/finance/journal', financeReports.journal.postingReport);
- [ ]   app.get('/reports/finance/account_statement', financeReports.accountStatement.report);
- [ ]   app.get('/reports/finance/general_ledger/', financeReports.generalLedger.report);
- [ ]   app.get('/reports/finance/creditors/aged', financeReports.creditors.aged);
- [ ]   app.get('/reports/finance/purchases', financeReports.purchases.report);
- [ ]   app.get('/reports/finance/ohada_balance_sheet', financeReports.ohadaBalanceSheet.document);
- [ ]   app.get('/reports/finance/ohada_profit_loss', financeReports.ohadaProfitLoss.document);
- [ ]   app.get('/reports/finance/account_reference', financeReports.accountReference.report);
- [ ]   app.get('/reports/finance/fee_center', financeReports.feeCenter.report);
- [ ]   app.get('/reports/finance/annual-clients-report', financeReports.annualClientsReport);
- [ ]   app.get('/reports/finance/employeeStanding/', financeReports.employee);
- [ ]   app.get('/reports/finance/break_even', financeReports.breakEven.report);
- [ ]   app.get('/reports/finance/break_even_fee_center', financeReports.breakEvenFeeCenter.report);
- [ ]   app.get('/reports/finance/operating', financeReports.operating.document);
- [ ]   app.get('/reports/finance/debtorSummary', debtorSummaryReport.summaryReport);
- [ ]   app.get('/reports/finance/clientDebts', clientDebts.report);
- [ ]   app.get('/reports/finance/clientSupport', clientSupport.report);
- [ ]   app.get('/reports/finance/realizedProfit', realizedProfit.report);
- [ ]   app.get('/reports/finance/systemUsageStat', systemUsage.document);
- [ ]   app.get('/reports/finance/analysis_auxiliary_cashbox', financeReports.analysisAuxiliaryCashbox.report);
- [ ]   app.get('/reports/visits', medicalReports.visitsReports.document);
- [ ]   app.get('/reports/keys/:key', report.keys);
- [ ]   app.get('/reports/saved/:reportId', report.list);
- [ ]   app.get('/reports/archive/:uuid', report.sendArchived);
- [ ]   app.post('/reports/archive/:uuid/email', report.emailArchived);
- [ ]   app.delete('/reports/archive/:uuid', report.deleteArchived);
- [ ]   app.get('/dashboard/debtors', dashboardDebtors.getReport);
- [ ]   app.get('/patients/groups', patientGroups.list);
- [ ]   app.get('/patients/groups/:uuid', patientGroups.detail);
- [ ]   app.post('/patients/groups', patientGroups.create);
- [ ]   app.put('/patients/groups/:uuid', patientGroups.update);
- [ ]   app.delete('/patients/groups/:uuid', patientGroups.remove);
- [ ]   app.get('/patients/search/name', patients.searchByName);
- [ ]   app.get('/patients/visits', patients.visits.list);
- [ ]   app.get('/patients/visits/:uuid', patients.visits.detail);
- [ ]   app.get('/patients/merge/count_employees', patients.merge.countEmployees);
- [ ]   app.post('/patients/merge', patients.merge.mergePatients);
- [ ]   app.get('/patients', patients.read);
- [ ]   app.post('/patients', patients.create);
- [ ]   app.get('/patients/:uuid', patients.detail);
- [ ]   app.put('/patients/:uuid', patients.update);
- [ ]   app.get('/patients/:uuid/groups', patients.groups.list);
- [ ]   app.post('/patients/:uuid/groups', patients.groups.update);
- [ ]   app.get('/patients/hospital_number/:id/exists', patients.hospitalNumberExists);
- [ ]   app.get('/patients/:uuid/services', patients.invoicingFees);
- [ ]   app.get('/patients/:uuid/subsidies', patients.subsidies);
- [ ]   app.get('/patients/:uuid/documents', patients.documents.list);
- [ ]   app.post('/patients/:uuid/documents', upload.middleware('docs', 'documents'), patients.documents.create);
- [ ]   app.delete('/patients/:uuid/documents/all', patients.documents.deleteAll);
- [ ]   app.delete('/patients/:uuid/documents/:documentUuid', patients.documents.delete);
- [ ]   app.post('/patients/:uuid/pictures', upload.middleware('pics', 'pictures'), patients.pictures.set);
- [ ]   app.get('/patients/visits/:uuid', patients.visits.detail);
- [ ]   app.get('/patients/:uuid/visits/status', patients.visits.patientAdmissionStatus);
- [ ]   app.get('/patients/:patientUuid/visits/:uuid', patients.visits.detail);
- [ ]   app.get('/patients/:uuid/visits', patients.visits.listByPatient);
- [ ]   app.post('/patients/:uuid/visits/admission', patients.visits.admission);
- [ ]   app.post('/patients/:uuid/visits/discharge', patients.visits.discharge);
- [ ]   app.post('/patients/:uuid/visits/:patient_visit_uuid/transfer', patients.visits.transfer);
- [ ]   app.get('/patients/:uuid/finance/activity', patients.getFinancialStatus);
- [ ]   app.get('/patients/:uuid/finance/balance', patients.getDebtorBalance);
- [ ]   app.get('/barcode/:key', report.barcodeLookup);
- [ ]   app.get('/barcode/redirect/:key', report.barcodeRedirect);
- [ ]   app.get('/debtors/groups', debtorGroups.list);
- [ ]   app.get('/debtors/groups/:uuid', debtorGroups.detail);
- [ ]   app.get('/debtors/:uuid/invoices', debtors.invoices);
- [ ]   app.put('/debtors/:uuid', debtors.update);
- [ ]   app.get('/debtors/:uuid', debtors.detail);
- [ ]   app.get('/debtors', debtors.list);
- [ ]   app.get('/debtor_groups', debtorGroups.list);
- [ ]   app.get('/debtor_groups/:uuid', debtorGroups.detail);
- [ ]   app.get('/debtor_groups/history/:debtorUuid', debtorGroups.history);
- [ ]   app.get('/debtor_groups/:uuid/invoices', debtorGroups.invoices);
- [ ]   app.post('/debtor_groups', debtorGroups.create);
- [ ]   app.put('/debtor_groups/:uuid', debtorGroups.update);
- [ ]   app.delete('/debtor_groups/:uuid', debtorGroups.delete);
- [ ]   app.get('/users', users.list);
- [ ]   app.post('/users', users.create);
- [ ]   app.get('/users/:id', users.detail);
- [ ]   app.get('/users/:username/exists', users.exists);
- [ ]   app.put('/users/:id', users.update);
- [ ]   app.delete('/users/:id', users.delete);
- [ ]   app.get('/users/:id/projects', users.projects.list);
- [ ]   app.get('/users/:id/depots', users.depots.list);
- [ ]   app.post('/users/:id/depots', users.depots.create);
- [ ]   app.put('/users/:id/password', users.password);
- [ ]   app.get('/users/:id/cashboxes', users.cashboxes.list);
- [ ]   app.post('/users/:id/cashboxes', users.cashboxes.create);
- [ ]   app.get('/projects/:id', projects.detail);
- [ ]   app.put('/projects/:id', projects.update);
- [ ]   app.post('/projects', projects.create);
- [ ]   app.delete('/projects/:id', projects.delete);
- [ ]   app.get('/cashboxes', cashboxes.list);
- [ ]   app.get('/cashboxes/privileges', cashboxes.privileges);
- [ ]   app.get('/cashboxes/:id', cashboxes.detail);
- [ ]   app.post('/cashboxes', cashboxes.create);
- [ ]   app.put('/cashboxes/:id', cashboxes.update);
- [ ]   app.delete('/cashboxes/:id', cashboxes.delete);
- [ ]   app.get('/cashboxes/:id/currencies', cashboxes.currencies.list);
- [ ]   app.get('/cashboxes/:id/currencies/:currencyId', cashboxes.currencies.detail);
- [ ]   app.post('/cashboxes/:id/currencies', cashboxes.currencies.create);
- [ ]   app.put('/cashboxes/:id/currencies/:currencyId', cashboxes.currencies.update);
- [ ]   app.get('/cashboxes/:id/users', cashboxes.users);
- [ ]   app.get('/prices', priceList.list);
- [ ]   app.get('/prices/:uuid', priceList.details);
- [ ]   app.get('/prices/download/list', priceListPreport.downloadRegistry);
- [ ]   app.get('/prices/download/template', priceList.downloadTemplate);
- [ ]   app.get('/prices/download/filled_template', priceList.downloadFilledTemplate);
- [ ]   app.get('/prices/report/:uuid', financeReports.priceList);
- [ ]   app.post('/prices', priceList.create);
- [ ]   app.post('/prices/item', priceList.createItem);
- [ ]   app.post('/prices/item/import', upload.middleware('csv', 'file'), priceList.importItem);
- [ ]   app.put('/prices/:uuid', priceList.update);
- [ ]   app.delete('/prices/:uuid', priceList.delete);
- [ ]   app.delete('/prices/item/:uuid', priceList.deleteItem);
- [ ]   app.get('/cash', cash.read);
- [ ]   app.get('/cash/:uuid', cash.detail);
- [ ]   app.post('/cash', cash.create);
- [ ]   app.put('/cash/:uuid', cash.update);
- [ ]   app.get('/cash/checkin/:invoiceUuid', cash.checkInvoicePayment);
- [ ]   app.get('/enterprises', enterprises.list);
- [ ]   app.get('/enterprises/:id', enterprises.detail);
- [ ]   app.post('/enterprises', enterprises.create);
- [ ]   app.put('/enterprises/:id', enterprises.update);
- [ ]   app.get('/enterprises/:id/fiscal_start', fiscal.getEnterpriseFiscalStart);
- [ ]   app.post('/enterprises/:id/logo', upload.middleware('pics', 'logo'), enterprises.uploadLogo);
- [ ]   app.get('/employees/search', employees.search);
- [ ]   app.get('/employees', employees.list);
- [ ]   app.get('/employees/:uuid', employees.detail);
- [ ]   app.get('/employees/:uuid/advantage', employees.advantage);
- [ ]   app.post('/employees', employees.create);
- [ ]   app.post('/employees/patient_employee', employees.patientToEmployee);
- [ ]   app.put('/employees/:uuid', employees.update);
- [ ]   app.get('/invoicing_fees', invoicingFees.list);
- [ ]   app.get('/invoicing_fees/:id', invoicingFees.detail);
- [ ]   app.post('/invoicing_fees', invoicingFees.create);
- [ ]   app.put('/invoicing_fees/:id', invoicingFees.update);
- [ ]   app.delete('/invoicing_fees/:id', invoicingFees.delete);
- [ ]   app.get('/multiple_payroll', multiplePayroll.search);
- [ ]   app.get('/multiple_payroll/:id/configuration', multiplePayroll.configuration);
- [ ]   app.post('/multiple_payroll/:id/configuration', multiplePayroll.setConfiguration.config);
- [ ]   app.post('/multiple_payroll/:id/multiConfiguration', multiplePayroll.setMultiConfiguration.config);
- [ ]   app.post('/multiple_payroll/:id/commitment', multiplePayroll.makeCommitment.config);
- [ ]   app.get('/multiple_payroll_indice/', multiplePayrollIndice.read);
- [ ]   app.post('/multiple_payroll_indice/', multiplePayrollIndice.create);
- [ ]   app.get('/multiple_payroll_indice/parameters/:payroll_config_id', multiplePayrollIndice.parameters.detail);
- [ ]   app.post('/multiple_payroll_indice/parameters/', multiplePayrollIndice.parameters.create);
- [ ]   app.get('/multiple_payroll_indice/reports/', multiplePayrollIndice.reports.document);
- [ ]   app.get('/discounts', discounts.list);
- [ ]   app.get('/discounts/:id', discounts.detail);
- [ ]   app.post('/discounts', discounts.create);
- [ ]   app.put('/discounts/:id', discounts.update);
- [ ]   app.delete('/discounts/:id', discounts.delete);
- [ ]   app.get('/vouchers', vouchers.list);
- [ ]   app.get('/vouchers/:uuid', vouchers.detail);
- [ ]   app.post('/vouchers', vouchers.create);
- [ ]   app.get('/suppliers/search', suppliers.search);
- [ ]   app.get('/suppliers', suppliers.list);
- [ ]   app.get('/suppliers/:uuid', suppliers.detail);
- [ ]   app.post('/suppliers', suppliers.create);
- [ ]   app.put('/suppliers/:uuid', suppliers.update);
- [ ]   app.delete('/suppliers/:uuid', suppliers.remove);
- [ ]   app.post('/purchases', purchases.create);
- [ ]   app.get('/purchases', purchases.list);
- [ ]   app.get('/purchases/search', purchases.search);
- [ ]   app.get('/purchases/purchaseState', purchases.purchaseState);
- [ ]   app.get('/purchases/:uuid', purchases.detail);
- [ ]   app.put('/purchases/:uuid', purchases.update);
- [ ]   app.get('/purchases/:uuid/stock_status', purchases.stockStatus);
- [ ]   app.get('/purchases/:uuid/stock_balance', purchases.stockBalance);
- [ ]   app.get('/functions', functions.list);
- [ ]   app.get('/functions/:id', functions.detail);
- [ ]   app.post('/functions', functions.create);
- [ ]   app.put('/functions/:id', functions.update);
- [ ]   app.delete('/functions/:id', functions.delete);
- [ ]   app.get('/rubrics', rubrics.list);
- [ ]   app.get('/rubrics/:id', rubrics.detail);
- [ ]   app.post('/rubrics', rubrics.create);
- [ ]   app.post('/rubrics/import_indexes', rubrics.importIndexes);
- [ ]   app.put('/rubrics/:id', rubrics.update);
- [ ]   app.delete('/rubrics/:id', rubrics.delete);
- [ ]   app.get('/rubric_config', rubricConfig.list);
- [ ]   app.get('/rubric_config/:id', rubricConfig.detail);
- [ ]   app.post('/rubric_config', rubricConfig.create);
- [ ]   app.put('/rubric_config/:id', rubricConfig.update);
- [ ]   app.get('/rubric_config/:id/setting', rubricConfig.listConfig);
- [ ]   app.post('/rubric_config/:id/setting', rubricConfig.createConfig);
- [ ]   app.delete('/rubric_config/:id', rubricConfig.delete);
- [ ]   app.get('/grades', grades.list);
- [ ]   app.get('/grades/:uuid', grades.detail);
- [ ]   app.post('/grades', grades.create);
- [ ]   app.put('/grades/:uuid', grades.update);
- [ ]   app.delete('/grades/:uuid', grades.delete);
- [ ]   app.get('/holidays', holidays.list);
- [ ]   app.get('/holidays/:id', holidays.detail);
- [ ]   app.post('/holidays', holidays.create);
- [ ]   app.put('/holidays/:id', holidays.update);
- [ ]   app.delete('/holidays/:id', holidays.delete);
- [ ]   app.get('/offdays', offdays.list);
- [ ]   app.get('/offdays/:id', offdays.detail);
- [ ]   app.post('/offdays', offdays.create);
- [ ]   app.put('/offdays/:id', offdays.update);
- [ ]   app.delete('/offdays/:id', offdays.delete);
- [ ]   app.get('/iprTax', iprTax.list);
- [ ]   app.get('/iprTax/:id', iprTax.detail);
- [ ]   app.post('/iprTax', iprTax.create);
- [ ]   app.put('/iprTax/:id', iprTax.update);
- [ ]   app.delete('/iprTax/:id', iprTax.delete);
- [ ]   app.get('/iprTaxConfig', iprTax.listConfig);
- [ ]   app.get('/iprTaxConfig/:id', iprTax.detailConfig);
- [ ]   app.post('/iprTaxConfig', iprTax.createConfig);
- [ ]   app.put('/iprTaxConfig/:id', iprTax.updateConfig);
- [ ]   app.delete('/iprTaxConfig/:id', iprTax.deleteConfig);
- [ ]   app.get('/account_config', accountConfig.list);
- [ ]   app.get('/account_config/:id', accountConfig.detail);
- [ ]   app.post('/account_config', accountConfig.create);
- [ ]   app.put('/account_config/:id', accountConfig.update);
- [ ]   app.delete('/account_config/:id', accountConfig.delete);
- [ ]   app.get('/weekend_config', weekendConfig.list);
- [ ]   app.get('/weekend_config/:id', weekendConfig.detail);
- [ ]   app.post('/weekend_config', weekendConfig.create);
- [ ]   app.put('/weekend_config/:id', weekendConfig.update);
- [ ]   app.get('/weekend_config/:id/days', weekendConfig.listConfig);
- [ ]   app.delete('/weekend_config/:id', weekendConfig.delete);
- [ ]   app.get('/employee_config', employeeConfig.list);
- [ ]   app.get('/employee_config/:id', employeeConfig.detail);
- [ ]   app.post('/employee_config', employeeConfig.create);
- [ ]   app.put('/employee_config/:id', employeeConfig.update);
- [ ]   app.get('/employee_config/:id/setting', employeeConfig.listConfig);
- [ ]   app.post('/employee_config/:id/setting', employeeConfig.createConfig);
- [ ]   app.delete('/employee_config/:id', employeeConfig.delete);
- [ ]   app.post('/creditors/groups', creditorGroups.create);
- [ ]   app.get('/creditors/groups', creditorGroups.list);
- [ ]   app.get('/creditors/groups/:uuid', creditorGroups.detail);
- [ ]   app.put('/creditors/groups/:uuid', creditorGroups.update);
- [ ]   app.delete('/creditors/groups/:uuid', creditorGroups.remove);
- [ ]   app.get('/creditors', creditors.list);
- [ ]   app.get('/creditors/:uuid', creditors.detail);
- [ ]   app.post('/transaction_type', transactionType.create);
- [ ]   app.get('/transaction_type', transactionType.list);
- [ ]   app.get('/transaction_type/:id', transactionType.detail);
- [ ]   app.put('/transaction_type/:id', transactionType.update);
- [ ]   app.delete('/transaction_type/:id', transactionType.remove);
- [ ]   app.get('/finance/cashflow', financeReports.cashflow.report);
- [ ]   app.get('/stock/assign', stock.assign.list);
- [ ]   app.get('/stock/assign/:uuid', stock.assign.detail);
- [ ]   app.post('/stock/assign', stock.assign.create);
- [ ]   app.put('/stock/assign/:uuid', stock.assign.update);
- [ ]   app.put('/stock/assign/:uuid/remove', stock.assign.removeAssign);
- [ ]   app.delete('/stock/assign/:uuid/delete', stock.assign.deleteAssign);
- [ ]   app.get('/stock/import/template', stock.importing.downloadTemplate);
- [ ]   app.post('/stock/import', upload.middleware('csv', 'file'), upload.hasFilesToUpload, stock.importing.importStock);
- [ ]   app.get('/stock/flux', stock.listStockFlux);
- [ ]   app.post('/stock/lots/movements', stock.createMovement);
- [ ]   app.get('/stock/lots/movements', stock.listLotsMovements);
- [ ]   app.post('/stock/lots', stock.createStock);
- [ ]   app.get('/stock/lots', stock.listLots);
- [ ]   app.get('/stock/lots/origins', stock.listLotsOrigins);
- [ ]   app.get('/stock/lots/depots/', stock.listLotsDepot);
- [ ]   app.get('/stock/inventories/depots', stock.listInventoryDepot);
- [ ]   app.post('/stock/integration', stock.createIntegration);
- [ ]   app.get('/reports/stock/exit', stockReports.stockExitReport);
- [ ]   app.get('/reports/stock/entry', stockReports.stockEntryReport);
- [ ]   app.get('/reports/stock/lots', stockReports.stockLotsReport);
- [ ]   app.get('/reports/stock/movements', stockReports.stockMovementsReport);
- [ ]   app.get('/reports/stock/inventories', stockReports.stockInventoriesReport);
- [ ]   app.get('/reports/stock/inventory', stockReports.stockInventoryReport);
- [ ]   app.get('/reports/stock/value', stockReports.stockValue);
- [ ]   app.get('/receipts/stock/exit_patient/:document_uuid', stockReports.stockExitPatientReceipt);
- [ ]   app.get('/receipts/stock/exit_service/:document_uuid', stockReports.stockExitServiceReceipt);
- [ ]   app.get('/receipts/stock/exit_depot/:document_uuid', stockReports.stockExitDepotReceipt);
- [ ]   app.get('/receipts/stock/exit_loss/:document_uuid', stockReports.stockExitLossReceipt);
- [ ]   app.get('/receipts/stock/assign/:uuid', stockReports.stockAssignReceipt);
- [ ]   app.get('/receipts/stock/entry_depot/:document_uuid', stockReports.stockEntryDepotReceipt);
- [ ]   app.get('/receipts/stock/entry_purchase/:document_uuid', stockReports.stockEntryPurchaseReceipt);
- [ ]   app.get('/receipts/stock/entry_integration/:document_uuid', stockReports.stockEntryIntegrationReceipt);
- [ ]   app.get('/receipts/stock/entry_donation/:document_uuid', stockReports.stockEntryDonationReceipt);
- [ ]   app.get('/receipts/stock/adjustment/:document_uuid', stockReports.stockAdjustmentReceipt);
- [ ]   app.get('/stock/consumptions/average/:periodId', stock.getStockConsumptionAverage);
- [ ]   app.get('/stock/consumptions/:periodId', stock.getStockConsumption);
- [ ]   app.get('/stock/transfers', stock.getStockTransfers);
- [ ]   app.get('/install', install.checkBasicInstallExist);
- [ ]   app.post('/install', install.proceedInstall);
- [ ]   app.get('/diagnoses', diagnoses.list);
- [ ]   app.get('/roles', rolesCtrl.list);
- [ ]   app.get('/roles/:uuid', rolesCtrl.detail);
- [ ]   app.get('/roles/:uuid/units', rolesCtrl.units);
- [ ]   app.get('/roles/actions/:roleUuid', rolesCtrl.rolesAction);
- [ ]   app.get('/roles/actions/user/:action_id', rolesCtrl.hasAction);
- [ ]   app.get('/roles/user/:id', rolesCtrl.listForUser);
- [ ]   app.post('/roles', rolesCtrl.create);
- [ ]   app.put('/roles/:uuid', rolesCtrl.update);
- [ ]   app.delete('/roles/:uuid', rolesCtrl.remove);
- [ ]   app.post('/roles/affectUnits', rolesCtrl.assignUnitsToRole);
- [ ]   app.post('/roles/assignTouser', rolesCtrl.assignRolesToUser);
- [ ]   app.post('/roles/actions', rolesCtrl.assignActionToRole);
- [ ]   app.get('/departments', department.read);
- [ ]   app.get('/departments/:uuid', department.detail);
- [ ]   app.post('/departments', department.create);
- [ ]   app.delete('/departments/:uuid', department.delete);
- [ ]   app.put('/departments/:uuid', department.update);
- [ ]   app.get('/entities/types', entities.types.list);
- [ ]   app.get('/entities/types/:id', entities.types.details);
- [ ]   app.put('/entities/types/:id', entities.types.update);
- [ ]   app.delete('/entities/types/:id', entities.types.remove);
- [ ]   app.post('/entities/types', entities.types.create);
- [ ]   app.get('/entities/groups', entities.groups.list);
- [ ]   app.get('/entities/groups/:uuid', entities.groups.details);
- [ ]   app.put('/entities/groups/:uuid', entities.groups.update);
- [ ]   app.delete('/entities/groups/:uuid', entities.groups.remove);
- [ ]   app.post('/entities/groups/', entities.groups.create);
- [ ]   app.get('/entities', entities.list);
- [ ]   app.get('/entities/:uuid', entities.details);
- [ ]   app.put('/entities/:uuid', entities.update);
- [ ]   app.delete('/entities/:uuid', entities.remove);
- [ ]   app.post('/entities', entities.create);
- [ ]   app.get('/tags', tags.read);
- [ ]   app.get('/tags/:uuid', tags.detail);
- [ ]   app.post('/tags', tags.create);
- [ ]   app.delete('/tags/:uuid', tags.delete);
- [ ]   app.put('/tags/:uuid', tags.update);
- [ ]   app.get('/fee_center', feeCenter.list);
- [ ]   app.get('/fee_center/:id', feeCenter.detail);
- [ ]   app.post('/fee_center', feeCenter.create);
- [ ]   app.put('/fee_center/:id', feeCenter.update);
- [ ]   app.delete('/fee_center/:id', feeCenter.delete);
- [ ]   app.get('/distribution_fee_center', distributionConfiguration.configuration);
- [ ]   app.get('/distribution_fee_center/getDistributed', distributionGetDistributed.getDistributed);
- [ ]   app.get('/distribution_fee_center/getDistributionKey', distributionGetDistributionKey.getDistributionKey);
- [ ]   app.post('/distribution_fee_center/proceed', distributionProceed.proceed);
- [ ]   app.post('/distribution_fee_center/breakDown', distributionBreakDown.breakDown);
- [ ]   app.post('/distribution_fee_center/automatic', distributionAutomatic.automatic);
- [ ]   app.post('/distribution_fee_center/distributionKey', setDistributionKey.setting);
- [ ]   app.post('/distribution_fee_center/resetKey', setDistributionKey.resetKey);
- [ ]   app.get('/wards', ward.read);
- [ ]   app.get('/wards/:uuid', ward.detail);
- [ ]   app.post('/wards', ward.create);
- [ ]   app.put('/wards/:uuid', ward.update);
- [ ]   app.delete('/wards/:uuid', ward.delete);
- [ ]   app.get('/rooms', room.read);
- [ ]   app.get('/rooms/:uuid', room.detail);
- [ ]   app.post('/rooms', room.create);
- [ ]   app.put('/rooms/:uuid', room.update);
- [ ]   app.delete('/rooms/:uuid', room.delete);
- [ ]   app.get('/beds', bed.read);
- [ ]   app.get('/beds/:id', bed.detail);
- [ ]   app.post('/beds', bed.create);
- [ ]   app.put('/beds/:id', bed.update);
- [ ]   app.delete('/beds/:id', bed.delete);
- [ ]   app.get('/lots/:uuid', lots.details);
- [ ]   app.put('/lots/:uuid', lots.update);
- [ ]   app.get('/lots/:uuid/assignments/:depot_uuid', lots.assignments);
- [ ]   app.get('/account_reference_type', accountReferenceType.list);
- [ ]   app.get('/account_reference_type/:id', accountReferenceType.detail);
- [ ]   app.post('/account_reference_type', accountReferenceType.create);
- [ ]   app.put('/account_reference_type/:id', accountReferenceType.update);
- [ ]   app.delete('/account_reference_type/:id', accountReferenceType.delete);
- [ ]   app.get('/discharge_types', dischargeTypes.list);
- [ ]   app.get('/indicators', indicators.read);
- [ ]   app.get('/indicators/status', indicators.status.list);
- [ ]   app.get('/indicators/types', indicators.types.list);
- [ ]   app.get('/indicators/hospitalization/:uuid', indicators.hospitalization.detail);
- [ ]   app.post('/indicators/hospitalization', indicators.hospitalization.create);
- [ ]   app.put('/indicators/hospitalization/:uuid', indicators.hospitalization.update);
- [ ]   app.delete('/indicators/hospitalization/:uuid', indicators.hospitalization.delete);
- [ ]   app.get('/indicators/staff/:uuid', indicators.personel.detail);
- [ ]   app.post('/indicators/staff', indicators.personel.create);
- [ ]   app.put('/indicators/staff/:uuid', indicators.personel.update);
- [ ]   app.delete('/indicators/staff/:uuid', indicators.personel.delete);
- [ ]   app.get('/indicators/finances/:uuid', indicators.finances.detail);
- [ ]   app.post('/indicators/finances', indicators.finances.create);
- [ ]   app.put('/indicators/finances/:uuid', indicators.finances.update);
- [ ]   app.delete('/indicators/finances/:uuid', indicators.finances.delete);
- [ ]   app.get('/break_even_reference', breakEvenReference.list);
- [ ]   app.get('/break_even_reference/:id', breakEvenReference.detail);
- [ ]   app.post('/break_even_reference', breakEvenReference.create);
- [ ]   app.put('/break_even_reference/:id', breakEvenReference.update);
- [ ]   app.delete('/break_even_reference/:id', breakEvenReference.delete);
- [ ]   app.get('/indicators/dashboards', dashboard.getIndicators);
- [ ]   app.get('/reports/indicatorsReport', indicatorRerpor.report);
- [ ]   app.get('/crons', cron.list);
- [ ]   app.get('/crons/:id', cron.details);
- [ ]   app.post('/crons', cron.create);
- [ ]   app.put('/crons/:id', cron.update);
- [ ]   app.delete('/crons/:id', cron.remove);
- [ ]   app.get('/cron_email_reports', cronEmailReport.list);
- [ ]   app.get('/cron_email_reports/:id', cronEmailReport.details);
- [ ]   app.post('/cron_email_reports', cronEmailReport.create);
- [ ]   app.post('/cron_email_reports/:id', cronEmailReport.send);
- [ ]   app.delete('/cron_email_reports/:id', cronEmailReport.remove);
- [ ]   app.get('/data_collector_management', dataCollectorManagement.list);
- [ ]   app.get('/data_collector_management/:id', dataCollectorManagement.detail);
- [ ]   app.post('/data_collector_management', dataCollectorManagement.create);
- [ ]   app.put('/data_collector_management/:id', dataCollectorManagement.update);
- [ ]   app.delete('/data_collector_management/:id', dataCollectorManagement.delete);
- [ ]   app.get('/choices_list_management', choicesListManagement.list);
- [ ]   app.get('/choices_list_management/:id', choicesListManagement.detail);
- [ ]   app.post('/choices_list_management', choicesListManagement.create);
- [ ]   app.put('/choices_list_management/:id', choicesListManagement.update);
- [ ]   app.delete('/choices_list_management/:id', choicesListManagement.delete);
- [ ]   app.get('/survey_form', surveyForm.list);
- [ ]   app.get('/survey_form/listSurveyformtype', surveyForm.listSurveyformtype);
- [ ]   app.get('/survey_form/:id', surveyForm.detail);
- [ ]   app.post('/survey_form', surveyForm.create);
- [ ]   app.put('/survey_form/:id', surveyForm.update);
- [ ]   app.delete('/survey_form/:id', surveyForm.delete);
- [ ]   app.get('/fill_form/:uuid', fillFormsData.detail);
- [ ]   app.post('/fill_form', fillFormsData.create);
- [ ]   app.post('/fill_form/restoreImage', fillFormsData.restoreImage);
- [ ]   app.post('/fill_form/:uuid/:key/image', upload.middleware('pics', 'image'), fillFormsData.uploadImage);
- [ ]   app.put('/fill_form/:uuid', fillFormsData.update);
- [ ]   app.get('/display_metadata', displayMetadata.list);
- [ ]   app.get('/display_metadata/card', displayMetadataReport.metadataCard);
- [ ]   app.get('/data_kit/report', displayMetadataReport.reportMetadata);
- [ ]   app.delete('/display_metadata/:uuid', displayMetadata.delete);
