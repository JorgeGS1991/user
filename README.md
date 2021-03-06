# rbacjs

### Role base access control
[![Build Status](https://travis-ci.org/zahorovskyi/rbacjs.svg?branch=master)](https://travis-ci.org/zahorovskyi/rbacjs)
[![NPM version](https://badge.fury.io/js/rbacjs.svg)](https://nodei.co/npm/rbacjs/)
[![dependencies Status](https://david-dm.org/zahorovskyi/rbacjs/status.svg)](https://david-dm.org/zahorovskyi/rbacjs)
[![devDependencies Status](https://david-dm.org/zahorovskyi/rbacjs/dev-status.svg)](https://david-dm.org/zahorovskyi/rbacjs?type=dev)
[![contributions welcome](https://img.shields.io/badge/contributions-welcome-brightgreen.svg?style=flat)](https://github.com/zahorovskyi/rbacjs/issues)
[![codecov.io Code Coverage](https://img.shields.io/codecov/c/github/zahorovskyi/rbacjs.svg)](https://codecov.io/github/zahorovskyi/rbacjs?branch=master)

[![NPM](https://nodei.co/npm/rbacjs.png?downloads=true&downloadRank=true&stars=true)](https://nodei.co/npm/rbacjs/)

Super simple role base access control library.
The easiest API and implementation.
Can be used on the ***client*** and ***server*** side.

#### Examples:
[Basic example](https://github.com/zahorovskyi/rbacjs/blob/master/examples/simple.js)

[Redux](https://github.com/zahorovskyi/rbacjs/blob/master/examples/redux.js)

[ExpressJS](https://github.com/zahorovskyi/rbacjs/blob/master/examples/express.js)

#### API:

Initialize RBAC with config:
``` javascript
interface IRBACConfig {
    rolesConfig: [                      // array with roles configurations
        {
            roles: string[],
            permissions: string[]
        }
    ];
    debug?: boolean;               // do not print warnings in console, by default true
}

const rbacConfig: IRBACConfig = {
    rolesConfig: [
        {
            roles: ['ROLE'],
            permissions: ['PERMISSION_ID']
        }
    ]
};
const rbac = new RBAC(rbacConfig);
```
Get roles list for user:
``` javascript
rbac.getUserRoles(userId: string) => string[] | Error;
```
Add user to RBAC with roles:
``` javascript
rbac.addUserRoles(userId: string, roles: string[]) => void | Error;
```
Remove users roles (in case if roles parameter is not defined, will be removed all roles for userId):
``` javascript
rbac.removeUserRoles(userId: string, roles?: string[]) => void | Error;
```
Check permission for user:
``` javascript
rbac.isAllowed(userId: string, permissionId: string) => boolead | Error;
```
Extend role:
``` javascript
rbac.extendRole(role: string, extendingRoles: string[]) => void | Error;

// example, expand manager role with viewers and users permissions:
rbac.extendRole('manager', extendingRoles: ['viewer', 'user']);
```
Middleware method, invoke success callback in case if user have permission or error callback if not:
``` javascript
rbac.middleware(
    params: {
        userId: string;
        permissionId: string;
    },
    error: () => void,
    success: () => void
)
```

##### Express middleware example:
``` javascript
app.use((req, res, next) => {
    rbac.middleware(
        {
            userId: req.body.userId,
            permissionId: req.body.permissionId
        },
        () => {
            res.status(403).send('access denied');
        },
        next
    );
});
```
##### Redux middleware example:
``` javascript
const rbacMiddleware = store => next => action => {
    rbac.middleware(
        {
            userId: action.payload.userId,
            permissionId: action.payload.permissionId
        },
        () => {
            next({
               type: ACTION_ACCESS_DENIED,
               payload: action
            });
        },
        () => {
            next(action);
        }
    );
};

const store = createStore(
    // ...
    applyMiddleware(
        // ...
        rbacMiddleware
    )
);
```
