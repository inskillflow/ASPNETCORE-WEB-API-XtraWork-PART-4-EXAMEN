# EXAMEN FINAL - PARTIE 1

## ASP.NET Core : Fondations, JWT, et API REST


# Question 1

Dans une architecture Clean, quelle couche est responsable de la logique metier ?

* [ ] Controller
* [ ] Service
* [ ] Repository
* [ ] DbContext

<br/>
<br/>

# Question 2
Qu'est-ce qu'Entity Framework Core ?

* [ ] Un framework pour creer des API
* [ ] Un ORM qui mappe les objets C# vers des tables SQL
* [ ] Un outil de migration de base de donnees
* [ ] Un systeme d'authentification

<br/>
<br/>

# Question 3
Quel cycle de vie utilise-t-on pour un DbContext dans ASP.NET Core ?

* [ ] Singleton
* [ ] Transient
* [ ] Scoped
* [ ] Static

<br/>
<br/>

# Question 4
Ou configure-t-on la Dependency Injection dans ASP.NET Core ?

* [ ] Dans le constructeur des controllers
* [ ] Dans Program.cs avec builder.Services
* [ ] Dans appsettings.json
* [ ] Dans le DbContext

<br/>
<br/>

### Question 5
Que fait la methode SaveChangesAsync() dans Entity Framework ?

* [ ] Sauvegarde un fichier
* [ ] Commit les changements du DbContext vers la base de donnees
* [ ] Cree une sauvegarde de la base
* [ ] Enregistre les logs

<br/>
<br/>


<br/>
<br/>

# Question 6
Dans XtraWork, qui accede directement a la base de donnees ?

* [ ] Controller
* [ ] Service
* [ ] Repository
* [ ] Entity

<br/>
<br/>

### Question 7
Que signifie l'annotation [Required] sur une propriete d'entite ?

* [ ] La propriete doit etre initialisee dans le constructeur
* [ ] La colonne SQL ne peut pas etre NULL
* [ ] La propriete est obligatoire pour les tests
* [ ] La propriete doit etre unique

<br/>
<br/>

### Question 8
Pourquoi utiliser async/await dans les methodes Repository ?

* [ ] C'est obligatoire en ASP.NET Core
* [ ] Pour ne pas bloquer le thread pendant les operations I/O
* [ ] Pour ameliorer la securite
* [ ] Pour utiliser moins de memoire

<br/>
<br/>

### Question 9
Quelle commande applique les migrations a la base de donnees ?

* [ ] dotnet ef migrations add
* [ ] dotnet ef database create
* [ ] dotnet ef database update
* [ ] dotnet ef schema apply


<br/>
<br/>



# Question 10
Quelle methode ASP.NET Core configure l'authentification JWT ?

* [ ] builder.Services.AddJwt()
* [ ] builder.Services.AddAuthentication()
* [ ] builder.Services.ConfigureJwt()
* [ ] builder.Services.UseJwtBearer()

<br/>
<br/>

### Question 11
Dans quel ordre doivent etre les middlewares ?

* [ ] UseAuthorization puis UseAuthentication
* [ ] UseAuthentication puis UseAuthorization
* [ ] L'ordre n'a pas d'importance
* [ ] UseAuthentication seulement


<br/>
<br/>

# Question 12
Que fait l'attribut [Authorize] sur un controller ?

* [ ] Autorise tous les utilisateurs
* [ ] Necessite un JWT valide pour acceder
* [ ] Genere un JWT
* [ ] Verifie le mot de passe

<br/>
<br/>

# Question 13
Comment specifier qu'un endpoint est accessible seulement aux Admins ?

* [ ] [Authorize]
* [ ] [Authorize(Roles = "Admin")]
* [ ] [AdminOnly]
* [ ] [RequireRole("Admin")]

<br/>
<br/>

# Question 14
Dans XtraWork, ou le frontend envoie-t-il le JWT ?

* [ ] Dans l'URL
* [ ] Dans le body de la requete
* [ ] Dans le header Authorization
* [ ] Dans un cookie

<br/>
<br/>

# Question 15
Quelle methode retourne HTTP 404 Not Found ?

* [ ] Missing()
* [ ] NotFound()
* [ ] Error404()
* [ ] ResourceNotFound()

<br/>
<br/>

# Question 16
Avec [ApiController], que se passe-t-il si ModelState.IsValid est false ?

* [ ] Exception levee
* [ ] 400 Bad Request automatique
* [ ] Execution continue
* [ ] 500 Internal Server Error

<br/>
<br/>

# Question 17
Quelle bibliotheque de validation utilise XtraWork ?

* [ ] DataAnnotations
* [ ] FluentValidation
* [ ] System.ComponentModel.DataAnnotations
* [ ] ValidationHelper

<br/>
<br/>

# Question 18
Quelle est la difference entre HTTP 401 et HTTP 403 ?

* [ ] Pas de difference
* [ ] 401 = pas authentifie, 403 = pas autorise
* [ ] 401 = erreur serveur, 403 = erreur client
* [ ] 401 = token invalide, 403 = token expire

<br/>
<br/>

# Question 19
Dans XtraWork, quelle exception est levee si une ressource n'existe pas ?

* [ ] ResourceNotFoundException
* [ ] NotFoundException
* [ ] EntityNotFoundException
* [ ] MissingException

<br/>
<br/>

# Question 20
Dans une API REST, les URLs doivent contenir :

* [ ] Des verbes (getEmployees, createEmployee)
* [ ] Des noms de ressources (employees, titles)
* [ ] Des actions (retrieve, insert)
* [ ] Peu importe



