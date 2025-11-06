# EXAMEN PRATIQUE - TROUBLESHOOTING CORS


## CAS 1 : Origin Not Allowed

### Scenario

**Frontend (React) :**
```javascript
// http://localhost:3000
fetch('https://localhost:7033/api/employees', {
  method: 'GET',
  headers: {
    'Content-Type': 'application/json'
  }
});
```

**Backend (ASP.NET Core) :**
```csharp
// Program.cs
var builder = WebApplication.CreateBuilder(args);
builder.Services.AddControllers();

var app = builder.Build();
app.UseAuthentication();
app.UseAuthorization();
app.MapControllers();
app.Run();
```

### Erreur dans la console

```
Access to fetch at 'https://localhost:7033/api/employees' from origin 
'http://localhost:3000' has been blocked by CORS policy: 
No 'Access-Control-Allow-Origin' header is present on the requested resource.
```

---

### Question 1.1 : Quelle est la cause principale ?

* [ ] Le frontend utilise HTTP au lieu de HTTPS
* [ ] Le backend n'a pas configure CORS
* [ ] Le port 7033 est incorrect
* [ ] Le header Content-Type n'est pas autorise

---

### Question 1.2 : Quelle solution corrige le probleme ?

* [ ] Changer le frontend en HTTPS
* [ ] Ajouter AddCors et UseCors dans Program.cs
* [ ] Utiliser le meme port pour frontend et backend
* [ ] Retirer le header Content-Type

---

## CAS 2 : Credentials Mode Error

### Scenario

**Frontend (Vue.js) :**
```javascript
// http://localhost:8080
const token = localStorage.getItem('token');
fetch('https://api.example.com/employees', {
  method: 'GET',
  headers: {
    'Authorization': `Bearer ${token}`
  },
  credentials: 'include'  // ← Envoie cookies et Authorization
});
```

**Backend (ASP.NET Core) :**
```csharp
builder.Services.AddCors(options =>
{
    options.AddPolicy("MyPolicy", policy =>
    {
        policy.AllowAnyOrigin()      // ← Wildcard *
              .AllowAnyHeader()
              .AllowAnyMethod()
              .AllowCredentials();   // ← Avec credentials
    });
});

app.UseCors("MyPolicy");
```

### Erreur dans la console

```
Access to fetch at 'https://api.example.com/employees' from origin 
'http://localhost:8080' has been blocked by CORS policy: 
The value of the 'Access-Control-Allow-Origin' header in the response 
must not be the wildcard '*' when the request's credentials mode is 'include'.
```

---

### Question 2.1 : Pourquoi cette erreur se produit-elle ?

* [ ] Le token JWT est invalide
* [ ] On ne peut pas utiliser AllowAnyOrigin avec AllowCredentials
* [ ] Le frontend doit utiliser fetch sans credentials
* [ ] Le backend doit retirer AllowAnyHeader

---

### Question 2.2 : Quelle est la solution correcte ?

* [ ] Retirer credentials: 'include' du frontend
* [ ] Remplacer AllowAnyOrigin par WithOrigins("http://localhost:8080")
* [ ] Retirer AllowCredentials du backend
* [ ] Utiliser cookies au lieu de Authorization header

---

## CAS 3 : Preflight Failure

### Scenario

**Frontend (Angular) :**
```typescript
// http://localhost:4200
this.http.delete(`https://localhost:7033/api/employees/${id}`, {
  headers: new HttpHeaders({
    'Authorization': `Bearer ${token}`,
    'Content-Type': 'application/json'
  })
}).subscribe();
```

**Backend (ASP.NET Core) :**
```csharp
builder.Services.AddCors(options =>
{
    options.AddPolicy("AngularPolicy", policy =>
    {
        policy.WithOrigins("http://localhost:4200")
              .AllowAnyHeader()
              .AllowAnyMethod()
              .AllowCredentials();
    });
});

var app = builder.Build();

// Ordre des middlewares
app.UseAuthentication();       // ← Auth en premier
app.UseAuthorization();
app.UseCors("AngularPolicy");  // ← CORS apres Auth
app.MapControllers();
```

### Erreur dans la console

```
Access to XMLHttpRequest at 'https://localhost:7033/api/employees/1' from origin 
'http://localhost:4200' has been blocked by CORS policy: 
Response to preflight request doesn't pass access control check: 
It does not have HTTP ok status.
```

---

### Question 3.1 : Quelle est la cause de cette erreur ?

* [ ] La methode DELETE n'est pas autorisee
* [ ] UseCors est appele apres UseAuthentication
* [ ] L'origine Angular n'est pas correcte
* [ ] Le header Authorization manque dans WithHeaders

---

### Question 3.2 : Quel est l'ordre correct des middlewares ?

* [ ] UseAuthentication → UseAuthorization → UseCors
* [ ] UseCors → UseAuthentication → UseAuthorization
* [ ] UseAuthorization → UseCors → UseAuthentication
* [ ] L'ordre n'a pas d'importance

---

### Question 3.3 : Pourquoi cet ordre est-il crucial ?

* [ ] Pour des raisons de performance
* [ ] Les requetes OPTIONS (preflight) ne contiennent pas de JWT
* [ ] Pour eviter les attaques XSS
* [ ] C'est une convention ASP.NET Core

---

## CAS 4 : Method Not Allowed

### Scenario

**Frontend (Next.js) :**
```typescript
// http://localhost:3000
const response = await fetch('https://localhost:7033/api/employees', {
  method: 'PUT',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': `Bearer ${token}`
  },
  body: JSON.stringify({ name: 'John Updated' })
});
```

**Backend (ASP.NET Core) :**
```csharp
builder.Services.AddCors(options =>
{
    options.AddPolicy("NextPolicy", policy =>
    {
        policy.WithOrigins("http://localhost:3000")
              .AllowAnyHeader()
              .WithMethods("GET", "POST", "DELETE");  // ← PUT manquant
    });
});

app.UseCors("NextPolicy");
```

### Erreur dans la console

```
Access to fetch at 'https://localhost:7033/api/employees' from origin 
'http://localhost:3000' has been blocked by CORS policy: 
Method PUT is not allowed by Access-Control-Allow-Methods in preflight response.
```

---

### Question 4.1 : Quelle est la cause ?

* [ ] Le controller n'a pas de methode PUT
* [ ] WithMethods ne contient pas PUT
* [ ] PUT n'est pas une methode HTTP valide
* [ ] Le frontend doit utiliser POST au lieu de PUT

---

### Question 4.2 : Quelle solution corrige le probleme ?

* [ ] Ajouter "PUT" dans WithMethods
* [ ] Utiliser AllowAnyOrigin au lieu de WithOrigins
* [ ] Changer PUT en POST dans le frontend
* [ ] Retirer AllowAnyHeader

---

## CAS 5 : Header Not Allowed

### Scenario

**Frontend (React) :**
```javascript
// http://localhost:3000
fetch('https://localhost:7033/api/employees', {
  method: 'GET',
  headers: {
    'Authorization': `Bearer ${token}`,
    'X-Custom-Header': 'my-value',
    'Content-Type': 'application/json'
  }
});
```

**Backend (ASP.NET Core) :**
```csharp
builder.Services.AddCors(options =>
{
    options.AddPolicy("ReactPolicy", policy =>
    {
        policy.WithOrigins("http://localhost:3000")
              .WithHeaders("content-type", "authorization")  // ← Headers specifiques
              .AllowAnyMethod();
    });
});
```

### Erreur dans la console

```
Access to fetch at 'https://localhost:7033/api/employees' from origin 
'http://localhost:3000' has been blocked by CORS policy: 
Request header field x-custom-header is not allowed by Access-Control-Allow-Headers 
in preflight response.
```

---

### Question 5.1 : Pourquoi l'erreur se produit-elle ?

* [ ] X-Custom-Header n'existe pas en HTTP
* [ ] WithHeaders ne contient pas x-custom-header
* [ ] Les headers personnalises sont interdits en CORS
* [ ] Le frontend doit utiliser un nom de header different

---

### Question 5.2 : Quelle est la meilleure solution ?

* [ ] Retirer X-Custom-Header du frontend
* [ ] Ajouter "x-custom-header" dans WithHeaders
* [ ] Remplacer WithHeaders par AllowAnyHeader
* [ ] Utiliser Authorization pour transmettre la valeur

---

## CAS 6 : Wrong Origin Configuration

### Scenario

**Frontend deploye en production :**
```
URL: https://app.example.com
```

**Backend (ASP.NET Core) :**
```csharp
builder.Services.AddCors(options =>
{
    options.AddPolicy("ProdPolicy", policy =>
    {
        policy.WithOrigins("http://app.example.com")  // ← HTTP au lieu de HTTPS
              .AllowAnyHeader()
              .AllowAnyMethod()
              .AllowCredentials();
    });
});
```

### Erreur dans la console

```
Access to fetch at 'https://api.example.com/employees' from origin 
'https://app.example.com' has been blocked by CORS policy: 
The 'Access-Control-Allow-Origin' header has a value 'http://app.example.com' 
that is not equal to the supplied origin.
```

---

### Question 6.1 : Quelle est l'erreur de configuration ?

* [ ] Le port est manquant dans WithOrigins
* [ ] HTTP ne correspond pas a HTTPS
* [ ] Le sous-domaine app. est incorrect
* [ ] AllowCredentials ne devrait pas etre utilise

---

### Question 6.2 : Quelle est la correction ?

* [ ] Changer le frontend en HTTP
* [ ] Changer WithOrigins en "https://app.example.com"
* [ ] Ajouter les deux HTTP et HTTPS dans WithOrigins
* [ ] Utiliser AllowAnyOrigin

---

## CAS 7 : Port Mismatch

### Scenario

**Frontend (Vue.js en dev) :**
```javascript
// Vite dev server : http://localhost:5173
fetch('https://localhost:7033/api/employees');
```

**Backend (ASP.NET Core) :**
```csharp
builder.Services.AddCors(options =>
{
    options.AddPolicy("VuePolicy", policy =>
    {
        policy.WithOrigins("http://localhost:3000")  // ← Port 3000 au lieu de 5173
              .AllowAnyHeader()
              .AllowAnyMethod();
    });
});
```

### Erreur dans la console

```
Access to fetch at 'https://localhost:7033/api/employees' from origin 
'http://localhost:5173' has been blocked by CORS policy: 
The 'Access-Control-Allow-Origin' header has a value 'http://localhost:3000' 
that is not equal to the supplied origin.
```

---

### Question 7.1 : Quelle est la cause ?

* [ ] Le frontend utilise le mauvais port pour l'API
* [ ] Le port dans WithOrigins ne correspond pas au port du frontend
* [ ] Vite utilise un port non standard
* [ ] Le backend doit ecouter sur le port 5173

---

### Question 7.2 : Quelle est la solution ?

* [ ] Changer le frontend pour utiliser le port 3000
* [ ] Changer WithOrigins en "http://localhost:5173"
* [ ] Ajouter ":5173" a la fin de l'URL de l'API
* [ ] Utiliser AllowAnyOrigin

---

## CAS 8 : Missing UseCors

### Scenario

**Frontend (React) :**
```javascript
fetch('https://localhost:7033/api/employees');
```

**Backend (ASP.NET Core) :**
```csharp
builder.Services.AddCors(options =>
{
    options.AddPolicy("ReactPolicy", policy =>
    {
        policy.WithOrigins("http://localhost:3000")
              .AllowAnyHeader()
              .AllowAnyMethod();
    });
});

var app = builder.Build();

// UseCors manquant !
app.UseAuthentication();
app.UseAuthorization();
app.MapControllers();
app.Run();
```

### Erreur dans la console

```
Access to fetch at 'https://localhost:7033/api/employees' from origin 
'http://localhost:3000' has been blocked by CORS policy: 
No 'Access-Control-Allow-Origin' header is present on the requested resource.
```

---

### Question 8.1 : Quelle est la cause ?

* [ ] La policy ReactPolicy n'existe pas
* [ ] AddCors est configure mais UseCors n'est pas appele
* [ ] L'origine est incorrecte
* [ ] Le frontend doit envoyer un header special

---

### Question 8.2 : Que faut-il ajouter ?

* [ ] app.UseRouting() avant MapControllers
* [ ] app.UseCors("ReactPolicy") avant UseAuthentication
* [ ] builder.Services.AddCorsPolicy()
* [ ] [EnableCors] sur les controllers

---

## CAS 9 : Postman Works, Browser Doesn't

### Scenario

**Test Postman :**
```
GET https://localhost:7033/api/employees
Authorization: Bearer eyJhbGc...
```
**Resultat** : ✅ 200 OK

**Test Browser (fetch) :**
```javascript
// http://localhost:3000
fetch('https://localhost:7033/api/employees', {
  headers: { 'Authorization': `Bearer ${token}` }
});
```
**Resultat** : ❌ CORS Error

**Backend :**
```csharp
// Aucune configuration CORS
var app = builder.Build();
app.UseAuthentication();
app.MapControllers();
```

---

### Question 9.1 : Pourquoi Postman fonctionne mais pas le navigateur ?

* [ ] Postman utilise un protocole different
* [ ] Postman n'applique pas la Same-Origin Policy
* [ ] Le navigateur bloque les requetes HTTPS
* [ ] Le token JWT n'est valide que pour Postman

---

### Question 9.2 : Quelle est la solution ?

* [ ] Utiliser Postman pour tous les tests
* [ ] Configurer CORS dans le backend
* [ ] Desactiver la securite du navigateur
* [ ] Utiliser un proxy pour contourner CORS

---

## CAS 10 : Multiple Origins

### Scenario

**Vous avez 3 frontends :**
- React Dev : `http://localhost:3000`
- React Prod : `https://app.example.com`
- Admin Vue : `http://localhost:8080`

**Backend actuel :**
```csharp
builder.Services.AddCors(options =>
{
    options.AddPolicy("AppPolicy", policy =>
    {
        policy.WithOrigins("http://localhost:3000")  // ← Une seule origine
              .AllowAnyHeader()
              .AllowAnyMethod()
              .AllowCredentials();
    });
});
```

### Probleme

React Dev fonctionne, mais Admin Vue et React Prod sont bloquees.

---

### Question 10.1 : Quelle est la cause ?

* [ ] On ne peut autoriser qu'une seule origine en CORS
* [ ] WithOrigins ne contient qu'une origine
* [ ] AllowCredentials bloque les autres origines
* [ ] Il faut une policy separee pour chaque frontend

---

### Question 10.2 : Quelle est la solution correcte ?

* [ ] Creer 3 policies differentes
* [ ] Utiliser AllowAnyOrigin
* [ ] Ajouter les 3 origines dans WithOrigins
* [ ] Deployer tous les frontends sur le meme domaine

---

## CAS 11 : Subdomain Issue

### Scenario

**Frontend :**
```
URL: https://app.example.com
```

**Backend API :**
```
URL: https://api.example.com
```

**Configuration CORS :**
```csharp
builder.Services.AddCors(options =>
{
    options.AddPolicy("AppPolicy", policy =>
    {
        policy.WithOrigins("https://example.com")  // ← Domaine parent
              .AllowAnyHeader()
              .AllowAnyMethod();
    });
});
```

### Erreur dans la console

```
Access to fetch at 'https://api.example.com/employees' from origin 
'https://app.example.com' has been blocked by CORS policy.
```

---

### Question 11.1 : Pourquoi l'erreur se produit-elle ?

* [ ] HTTPS ne fonctionne pas avec les sous-domaines
* [ ] example.com et app.example.com sont des origines differentes
* [ ] Le backend doit etre sur le meme sous-domaine
* [ ] WithOrigins ne supporte pas les sous-domaines

---

### Question 11.2 : Quelle est la solution ?

* [ ] Utiliser .WithOrigins("https://*.example.com") avec wildcard
* [ ] Changer WithOrigins en "https://app.example.com"
* [ ] Deployer frontend et backend sur le meme sous-domaine
* [ ] Utiliser SetIsOriginAllowedToAllowWildcardSubdomains

---

## CAS 12 : Environment-Specific Configuration

### Scenario

**Developpement :**
- Frontend : `http://localhost:3000`
- Backend : `https://localhost:7033`

**Production :**
- Frontend : `https://app.example.com`
- Backend : `https://api.example.com`

**Configuration actuelle (fonctionne en dev mais pas en prod) :**
```csharp
builder.Services.AddCors(options =>
{
    options.AddPolicy("AppPolicy", policy =>
    {
        policy.WithOrigins("http://localhost:3000")  // ← Seulement dev
              .AllowAnyHeader()
              .AllowAnyMethod()
              .AllowCredentials();
    });
});
```

---

### Question 12.1 : Quel est le probleme ?

* [ ] Production n'a pas de configuration CORS
* [ ] HTTPS ne fonctionne pas avec cette configuration
* [ ] Il faut desactiver CORS en production
* [ ] AllowCredentials est incompatible avec production

---

### Question 12.2 : Quelle est la meilleure approche ?

* [ ] Utiliser AllowAnyOrigin en production
* [ ] Configurer les origines selon l'environnement (IsDevelopment)
* [ ] Ajouter les deux origines dans WithOrigins
* [ ] Utiliser deux policies separees

---

## CAS 13 : MaxAge and Performance

### Scenario

**Votre API recoit beaucoup de requetes DELETE.**

**Network tab montre :**
```
OPTIONS /api/employees/1 → 204 No Content
DELETE /api/employees/1 → 200 OK

OPTIONS /api/employees/2 → 204 No Content
DELETE /api/employees/2 → 200 OK

OPTIONS /api/employees/3 → 204 No Content
DELETE /api/employees/3 → 200 OK
```

**Configuration actuelle :**
```csharp
builder.Services.AddCors(options =>
{
    options.AddPolicy("AppPolicy", policy =>
    {
        policy.WithOrigins("http://localhost:3000")
              .AllowAnyHeader()
              .AllowAnyMethod();
        // Pas de SetPreflightMaxAge
    });
});
```

---

### Question 13.1 : Quel est le probleme de performance ?

* [ ] DELETE est plus lent que GET
* [ ] Chaque DELETE declenche une preflight OPTIONS
* [ ] Le backend repond trop lentement
* [ ] Il y a trop de headers dans les requetes

---

### Question 13.2 : Quelle optimisation appliquer ?

* [ ] Retirer AllowAnyMethod pour eviter les preflights
* [ ] Ajouter SetPreflightMaxAge pour cacher les reponses preflight
* [ ] Utiliser GET au lieu de DELETE
* [ ] Desactiver CORS pour les DELETE

---

### Question 13.3 : Quelle est la configuration optimale ?

* [ ] SetPreflightMaxAge(TimeSpan.FromSeconds(30))
* [ ] SetPreflightMaxAge(TimeSpan.FromHours(24))
* [ ] SetPreflightMaxAge(TimeSpan.FromDays(365))
* [ ] Ne pas utiliser SetPreflightMaxAge

---

## CAS 14 : Controller-Level CORS

### Scenario

**Vous avez une API publique et une API privee.**

**Configuration globale :**
```csharp
// Program.cs - Pas de UseCors global
builder.Services.AddCors(options =>
{
    options.AddPolicy("PublicPolicy", policy =>
    {
        policy.AllowAnyOrigin().WithMethods("GET");
    });

    options.AddPolicy("PrivatePolicy", policy =>
    {
        policy.WithOrigins("https://app.example.com")
              .AllowAnyHeader()
              .AllowAnyMethod()
              .AllowCredentials();
    });
});
```

**Controllers :**
```csharp
[ApiController]
[Route("api/public")]
public class PublicController : ControllerBase
{
    [HttpGet]
    public IActionResult GetPublicData() => Ok("public");
}

[ApiController]
[Route("api/employees")]
[Authorize]
public class EmployeeController : ControllerBase
{
    [HttpGet]
    public IActionResult GetEmployees() => Ok(_service.GetAll());
}
```

### Probleme

Les deux endpoints sont bloques par CORS.

---

### Question 14.1 : Quelle est la cause ?

* [ ] Les policies PublicPolicy et PrivatePolicy sont mal configurees
* [ ] Les controllers n'ont pas l'attribut [EnableCors]
* [ ] Il faut absolument utiliser UseCors global
* [ ] [Authorize] bloque CORS

---

### Question 14.2 : Quelle est la solution ?

* [ ] Ajouter [EnableCors("PublicPolicy")] sur PublicController et [EnableCors("PrivatePolicy")] sur EmployeeController
* [ ] Retirer les policies et utiliser UseCors global
* [ ] Retirer [Authorize] de EmployeeController
* [ ] Fusionner les deux policies en une seule

---

## CAS 15 : Production Security Issue

### Scenario

**Configuration actuelle en production :**
```csharp
builder.Services.AddCors(options =>
{
    options.AddPolicy("ProdPolicy", policy =>
    {
        policy.AllowAnyOrigin()    // ← Dangereux !
              .AllowAnyHeader()
              .AllowAnyMethod();
    });
});

app.UseCors("ProdPolicy");
```

### Probleme

Un site malveillant peut appeler votre API.

---

### Question 15.1 : Quel est le risque de securite ?

* [ ] Les donnees sont exposees publiquement
* [ ] N'importe quel site peut appeler l'API
* [ ] Le backend est vulnerable aux attaques DDoS
* [ ] Les tokens JWT peuvent etre voles

---

### Question 15.2 : Quelle est la meilleure pratique en production ?

* [ ] Utiliser AllowAnyOrigin pour la flexibilite
* [ ] Specifier les origines exactes avec WithOrigins
* [ ] Desactiver CORS en production
* [ ] Utiliser un firewall pour bloquer les mauvaises origines

---

### Question 15.3 : Configuration securisee pour production ?

* [ ] AllowAnyOrigin + AllowAnyMethod
* [ ] WithOrigins("https://app.example.com") + AllowCredentials
* [ ] AllowAnyOrigin + WithMethods("GET")
* [ ] Pas de CORS en production

---

## Fin de l'examen

**Total** : 30 questions  
**Bareme** :
- 27-30 points : Excellent (90-100%)
- 24-26 points : Tres bien (80-89%)
- 21-23 points : Bien (70-79%) - Seuil de reussite
- 18-20 points : Passable (60-69%)
- < 18 points : Insuffisant - Echec

---

**Nom de l'etudiant** : ___________________

**Date** : ___________________

---

**Le corrige est dans le fichier 08-EXAMEN-TROUBLESHOOTING-CORS-CORRIGE.md**

