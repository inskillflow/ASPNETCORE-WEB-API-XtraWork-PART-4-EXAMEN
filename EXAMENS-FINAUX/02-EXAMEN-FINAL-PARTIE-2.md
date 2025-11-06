# EXAMEN NATIONAL - PARTIE 2

## ASP.NET Core : Services, DTOs, et CORS



## Partie A : Services et Repositories (Questions 1-10)

# Question 1

**Context** : Dans XtraWork, voici la structure en couches :
```
EmployeeController → EmployeeService → EmployeeRepository → DbContext
```

**Question** : Quelle est la responsabilite principale d'un Service ?

* [ ] Acces a la base de donnees
* [ ] Logique metier et orchestration
* [ ] Gerer les requetes HTTP
* [ ] Mapper les DTOs

<br/>
<br/>

# Question 2

**Context** : Voici un Repository typique dans XtraWork :
```csharp
public class EmployeeRepository
{
    private readonly XtraWorkContext _context;
    
    public async Task<List<Employee>> GetAllAsync()
    {
        return await _context.Employees.Include(e => e.Title).ToListAsync();
    }
}
```

**Question** : Quelle est la responsabilite principale d'un Repository ?

* [ ] Logique metier
* [ ] Validation des donnees
* [ ] Abstraction de l'acces aux donnees
* [ ] Generation de JWT

<br/>
<br/>


# Question 3

**Context** : Voici le constructeur d'EmployeeService :
```csharp
public class EmployeeService
{
    private readonly EmployeeRepository _employeeRepository;
    private readonly TitleRepository _titleRepository;
    
    public EmployeeService(
        EmployeeRepository employeeRepository,
        TitleRepository titleRepository)
    {
        _employeeRepository = employeeRepository;
        _titleRepository = titleRepository;
    }
}
```

**Question** : Combien de repositories un Service peut-il injecter ?

* [ ] Un seul obligatoirement
* [ ] Aucun
* [ ] Plusieurs (exemple : EmployeeService injecte 2 repositories)
* [ ] Tous les repositories existants

<br/>
<br/>


# Question 4
Que retourne Task<T> en C# ?

* [ ] Un thread
* [ ] Une promesse de valeur future (operation asynchrone)
* [ ] Un tableau
* [ ] Une exception

<br/>
<br/>


# Question 5
Pourquoi utiliser async/await dans les methodes Repository ?

* [ ] C'est obligatoire en ASP.NET Core
* [ ] Pour ne pas bloquer le thread pendant les operations I/O
* [ ] Pour ameliorer la securite
* [ ] Pour utiliser moins de memoire

<br/>
<br/>


# Question 6

**Context** : Extrait de EmployeeService.Create() :
```csharp
public async Task<EmployeeResponse> Create(EmployeeRequest request)
{
    // Verification que le titre existe
    var titleExists = await _titleRepository.ExistsAsync(request.TitleID);
    if (!titleExists)
    {
        throw new NotFoundException("Titre non trouve");
    }
    
    // Creer l'employe...
}
```

**Question** : Pourquoi verifier que le titre existe ?

* [ ] Pour optimiser les performances
* [ ] Pour respecter une regle metier (validation business)
* [ ] Pour eviter les erreurs SQL
* [ ] C'est optionnel

<br/>
<br/>


# Question 7
Ou doit se faire la validation des régles metier ?

* [ ] Dans le Controller
* [ ] Dans le Service
* [ ] Dans le Repository
* [ ] Dans l'Entity

<br/>
<br/>


# Question 8
Que fait la méthode ToListAsync() dans EF Core ?

* [ ] Cree une liste vide
* [ ] Execute la requete LINQ et retourne les resultats
* [ ] Convertit un tableau en liste
* [ ] Sauvegarde les donnees

<br/>
<br/>


# Question 9
Quelle est la différence entre un Service et un Repository ?

* [ ] Pas de difference
* [ ] Service = logique metier, Repository = acces donnees
* [ ] Service = HTTP, Repository = SQL
* [ ] Repository = async, Service = sync

<br/>
<br/>


# Question 10
Dans XtraWork, ou se trouve le mapping Entity vers DTO ?

* [ ] Dans le Controller
* [ ] Dans le Service
* [ ] Dans le Repository
* [ ] Dans l'Entity

<br/>
<br/>


## Partie B : DTOs et Mapping (Questions 11-20)

# Question 11

**Context** : Dans XtraWork, on a :
- Entities/ : Employee, Title, User (modeles de base de donnees)
- Requests/ : EmployeeRequest, LoginRequest (donnees entrantes API)
- Responses/ : EmployeeResponse, AuthResponse (donnees sortantes API)

**Question** : Que signifie DTO ?

* [ ] Data Type Object
* [ ] Data Transfer Object
* [ ] Database Transaction Object
* [ ] Dynamic Type Object

<br/>
<br/>


# Question 12

**Context** : Comparaison Entity vs DTO Response :
```csharp
// Entity (base de donnees)
public class Employee
{
    public Guid Id { get; set; }
    public string FirstName { get; set; }
    public Title Title { get; set; }  // Navigation property
}

// DTO Response (API)
public class EmployeeResponse
{
    public Guid Id { get; set; }
    public string FirstName { get; set; }
    public string TitleDescription { get; set; }  // Juste la description
}
```

**Question** : Pourquoi séparer les Entities des DTOs ?

* [ ] Pour rendre le code plus complexe
* [ ] Pour separer le modele de donnees du modele de communication API
* [ ] Pour respecter les conventions de nommage
* [ ] Pour ameliorer les performances

<br/>
<br/>


# Question 13
Quelle est la différence entre Request et Response DTOs ?

* [ ] Pas de difference
* [ ] Request = donnees entrantes, Response = donnees sortantes
* [ ] Request = GET, Response = POST
* [ ] Request = client, Response = serveur

<br/>
<br/>


# Question 14
Pourquoi EmployeeResponse n'a pas de navigation property Title complete ?

* [ ] Pour economiser de la memoire
* [ ] Pour eviter les references circulaires et controler ce qui est expose
* [ ] C'est une erreur
* [ ] Pour accelerer la serialisation

<br/>
<br/>


# Question 15

**Context** : Extrait d'EmployeeService.Create() :
```csharp
public async Task<EmployeeResponse> Create(EmployeeRequest request)
{
    // Mapping Request → Entity
    var employee = new Employee
    {
        Id = Guid.NewGuid(),
        FirstName = request.FirstName,
        LastName = request.LastName,
        BirthDate = request.BirthDate,
        Gender = request.Gender,
        TitleId = request.TitleId
    };
    
    var created = await _employeeRepository.CreateAsync(employee);
    return MapToResponse(createD);
}
```

**Question** : Ou se fait le mapping Request vers Entity ?

* [ ] Dans le Controller
* [ ] Dans le Service lors de la creation
* [ ] Dans le Repository
* [ ] Automatiquement par EF Core


<br/>
<br/>

# Question 16
Qu'est-ce que AutoMapper ?

* [ ] Un outil de navigation
* [ ] Une bibliotheque pour automatiser le mapping entre objets
* [ ] Un generateur de DTOs
* [ ] Un validateur

<br/>
<br/>


# Question 17
Pourquoi UserResponse n'expose pas PasswordHash ?

* [ ] Pour economiser de la bande passante
* [ ] Pour la securite - ne jamais exposer les hash de mots de passe
* [ ] C'est trop long
* [ ] Pour respecter RGPD

<br/>
<br/>


# Question 18
Dans EmployeeResponse, la propriête Age est :

* [ ] Stockee en base de donnees
* [ ] Calculee automatiquement a partir de BirthDate
* [ ] Envoyee par le client
* [ ] Generee par EF Core

<br/>
<br/>


# Question 19
Quelle annotation valide qu'un champ Request est requis ?

* [ ] [Mandatory]
* [ ] [Required]
* [ ] [NotNull]
* [ ] [Obligatoire]

<br/>
<br/>


### Question 20
Pourquoi mapper Employee.Title.Description vers EmployeeResponse.TitleDescription ?

* [ ] Pour gagner de la place
* [ ] Pour eviter de retourner l'objet Title complet avec toutes ses proprietes
* [ ] C'est obligatoire
* [ ] Pour la performance

<br/>
<br/>


## Partie C : CORS et Sécurité (Questions 21-30)

# Question 21

**Context** : Dans XtraWork, le frontend tourne sur http://localhost:3000 et le backend sur https://localhost:7033. Ce sont des origines differentes (ports differents).

**Question** : Que signifie CORS ?

* [ ] Cross-Origin Resource Security
* [ ] Cross-Origin Resource Sharing
* [ ] Cross-Origin Request Security
* [ ] Common Origin Resource Sharing

<br/>
<br/>


# Question 22
Pourquoi le navigateur bloque-t-il les requetes cross-origin par defaut ?

* [ ] Pour economiser la bande passante
* [ ] Pour la securite (Same-Origin Policy)
* [ ] C'est un bug
* [ ] Pour accelerer le chargement

<br/>
<br/>


# Question 23

**Context** : Configuration CORS dans XtraWork/Program.cs :
```csharp
builder.Services.AddCors(options =>
{
    options.AddPolicy("NextJsPolicy", policy =>
    {
        policy.WithOrigins("http://localhost:3000", "https://localhost:3000")
              .AllowAnyHeader()
              .AllowAnyMethod();
    });
});
```

**Question** : Quelle origine est autorisée par la politique CORS ?

* [ ] Toutes les origines
* [ ] http://localhost:3000 et https://localhost:3000
* [ ] Seulement https://localhost:3000
* [ ] Aucune origine

<br/>
<br/>


# Question 24
Que signifie AllowCredentials() dans la configuration CORS ?

* [ ] Autoriser les mots de passe
* [ ] Autoriser les cookies et headers Authorization (JWT)
* [ ] Autoriser tous les utilisateurs
* [ ] Autoriser HTTPS

<br/>
<br/>


# Question 25

**Context** : Pipeline de middleware dans XtraWork/Program.cs :
```csharp
app.UseCors("NextJsPolicy");  // CORS
app.UseAuthentication();      // Authentication
app.UseAuthorization();       // Authorization
app.MapControllers();
```

**Question** : Dans quel ordre doivent etre les middlewares CORS et Authentication ?

* [ ] Authentication puis CORS
* [ ] CORS puis Authentication
* [ ] L'ordre n'a pas d'importance
* [ ] CORS n'est pas un middleware

<br/>
<br/>


# Question 26
Qu'est-ce qu'une requete preflight ?

* [ ] Une requete de test avant deploiement
* [ ] Une requete OPTIONS envoyee par le navigateur avant la vraie requete
* [ ] La premiere requete de l'application
* [ ] Une requete de verification SSL

<br/>
<br/>


# Question 27
Pourquoi HTTPS est-il obligatoire en production pour une API avec JWT ?

* [ ] C'est plus rapide
* [ ] Pour chiffrer le token JWT pendant le transport
* [ ] Pour economiser de la memoire
* [ ] C'est une loi

<br/>
<br/>


# Question 28

**Context** : Dans AuthService.cs lors de l'inscription :
```csharp
var user = new User
{
    Username = request.Username,
    PasswordHash = BCrypt.Net.BCrypt.HashPassword(request.PassworD)
};
```

Lors de la connexion :
```csharp
if (!BCrypt.Net.BCrypt.Verify(request.Password, user.PasswordHash))
{
    throw new UnauthorizedAccessException("Mot de passe incorrect");
}
```

**Question** : Comment XtraWork hash-t-il les mots de passe ?

* [ ] MD5
* [ ] SHA-256
* [ ] BCrypt
* [ ] Pas de hash

<br/>
<br/>


# Question 29
Que fait UseHttpsRedirection() ?

* [ ] Active HTTPS
* [ ] Redirige les requetes HTTP vers HTTPS
* [ ] Verifie les certificats SSL
* [ ] Genere des certificats

<br/>
<br/>


# Question 30

**Context** : Dans XtraWork/appsettings.json :
```json
{
  "Jwt": {
    "Key": "7e6da5d9e5514ce288683845e6068df782a23f6c15fb43cd92e19b4147d8ce95"
  }
}
```

Cette cle fait 64 caracteres.

**Question** : Quelle est la longueur minimale recommandee pour une cle sécrête JWT ?

* [ ] 8 caracteres
* [ ] 16 caracteres
* [ ] 32 caracteres (minimum)
* [ ] 64 caracteres (recommande)

