# EXAMEN PRATIQUE - Analyse de Code XtraWork


# Question 1 

**Extrait** : XtraWork/Program.cs
```csharp
var connectionString = builder.Configuration.GetConnectionString("XtraWork");
builder.Services.AddDbContext<XtraWorkContext>(opt => opt.UseSqlServer(connectionString));

builder.Services.AddScoped<TitleRepository>();
builder.Services.AddScoped<EmployeeRepository>();
builder.Services.AddScoped<UserRepository>();

builder.Services.AddScoped<TitleService>();
builder.Services.AddScoped<EmployeeService>();
builder.Services.AddScoped<IAuthService, AuthService>();
```

**Questions** :
1. Expliquez pourquoi on utilise `AddScoped` et non `AddSingleton` 
2. Pourquoi AuthService utilise une interface `IAuthService` ? 
3. Quel serait le problème si on inversait l'ordre (Services avant Repositories) ? 

<br/>
<br/>

# Question 2

**Extrait** : XtraWork/Services/EmployeeService.cs
```csharp
public async Task<EmployeeResponse> Create(EmployeeRequest request)
{
    // Verifier que le titre existe
    var titleExists = await _titleRepository.ExistsAsync(request.TitleId);
    if (!titleExists)
    {
        throw new NotFoundException($"Titre avec l'ID {request.TitleId} non trouve");
    }
    
    var employee = new Employee
    {
        Id = Guid.NewGuid(),
        FirstName = request.FirstName,
        LastName = request.LastName,
        BirthDate = request.BirthDate,
        Gender = request.Gender,
        TitleId = request.TitleId,
        CreatedAt = DateTime.UtcNow
    };
    
    var createdEmployee = await _employeeRepository.CreateAsync(employee);
    
    var employeeWithTitle = await _employeeRepository.GetByIdAsync(createdEmployee.Id);
    
    return new EmployeeResponse
    {
        Id = employeeWithTitle.Id,
        FirstName = employeeWithTitle.FirstName,
        LastName = employeeWithTitle.LastName,
        BirthDate = employeeWithTitle.BirthDate,
        Gender = employeeWithTitle.Gender,
        TitleId = employeeWithTitle.TitleId,
        TitleDescription = employeeWithTitle.Title.Description,
        CreatedAt = employeeWithTitle.CreatedAt
    };
}
```

**Questions** :
1. Pourquoi vérifier si le titre existe AVANT de créer l'employe ? 
2. Expliquez pourquoi on fait GetByIdAsync apres CreateAsync 
3. Pourquoi mapper vers EmployeeResponse au lieu de retourner Employee directement ? 

<br/>
<br/>

# Question 3

**Extrait** : XtraWork/Repositories/EmployeeRepository.cs
```csharp
public async Task<List<Employee>> GetAllAsync()
{
    return await _context.Employees
        .Include(e => e.Title)
        .OrderBy(e => e.LastName)
        .ThenBy(e => e.FirstName)
        .ToListAsync();
}
```

**Questions** :
1. Qu'est-ce que `.Include(e => e.Title)` et pourquoi est-ce important ? 
2. Que se passerait-il sans `.Include()` ? 
3. Pourquoi utiliser `ToListAsync()` a la fin ? 

<br/>
<br/>

# Question 4

**Extrait** : XtraWork/Services/AuthService.cs
```csharp
private string GenerateJwtToken(User user)
{
    var key = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(_configuration["Jwt:Key"]!));
    var credentials = new SigningCredentials(key, SecurityAlgorithms.HmacSha256);
    
    var claims = new[]
    {
        new Claim(ClaimTypes.NameIdentifier, user.Id.ToString()),
        new Claim(ClaimTypes.Name, user.Username),
        new Claim(ClaimTypes.Email, user.Email),
        new Claim(ClaimTypes.Role, user.Role),
        new Claim("FirstName", user.FirstName),
        new Claim("LastName", user.LastName)
    };
    
    var token = new JwtSecurityToken(
        issuer: _configuration["Jwt:Issuer"],
        audience: _configuration["Jwt:Audience"],
        claims: claims,
        expires: DateTime.UtcNow.AddHours(24),
        signingCredentials: credentials
    );
    
    return new JwtSecurityTokenHandler().WriteToken(token);
}
```

**Questions** :
1. Expliquez le role de chaque claim ajoute au JWT 
2. Pourquoi utiliser HMAC-SHA256 pour la signature ? 
3. Quelle est la durée de vie du token et pourquoi 24 heures ? 

<br/>
<br/>

# Question 5 

**Extrait** : XtraWork/Controllers/EmployeeController.cs
```csharp
[ApiController]
[Route("api/[controller]")]
[Authorize]
[Produces("application/json")]
public class EmployeeController : ControllerBase
{
    private readonly EmployeeService _employeeService;
    
    public EmployeeController(EmployeeService employeeService)
    {
        _employeeService = employeeService;
    }
}
```

**Questions** :
1. Expliquez chaque attribut sur la classe ([ApiController], [Route], etc.) 
2. Qu'est-ce que l'injection de dépendance dans le constructeur ? 

<br/>
<br/>

# Question 6

**Extrait** : XtraWork/Controllers/EmployeeController.cs
```csharp
[HttpPost]
[ProducesResponseType(StatusCodes.Status201Created)]
[ProducesResponseType(StatusCodes.Status400BadRequest)]
public async Task<ActionResult<EmployeeResponse>> Create([FromBody] EmployeeRequest request)
{
    try
    {
        var response = await _employeeService.Create(request);
        return CreatedAtAction(nameof(Get), new { id = response.Id }, response);
    }
    catch (NotFoundException ex)
    {
        return BadRequest(new { message = ex.Message });
    }
}
```

**Questions** :
1. Que fait `CreatedAtAction` et pourquoi l'utiliser ? 
2. Pourquoi attraper `NotFoundException` et retourner `BadRequest` ? 
3. Expliquez `[FromBody]` 

<br/>
<br/>

# Question 7

**Extrait** : XtraWork/Program.cs
```csharp
builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(opt =>
    {
        opt.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = true,
            ValidIssuer = jwt["Issuer"],
            ValidateAudience = true,
            ValidAudience = jwt["Audience"],
            ValidateLifetime = true,
            ValidateIssuerSigningKey = true,
            IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(key))
        };
    });
```

**Questions** :
1. Expliquez chaque validation (ValidateIssuer, ValidateAudience, etc.) 
2. Pourquoi valider la cle de signature ? 

<br/>
<br/>

# Question 8 

**Extrait** : XtraWork/Program.cs
```csharp
app.UseCors("NextJsPolicy");
app.UseAuthentication();
app.UseAuthorization();
app.MapControllers();
```

**Questions** :
1. Pourquoi cet ordre spécifique ? 
2. Que se passerait-il si `UseAuthentication` venait avant `UseCors` ? 

<br/>
<br/>

# Question 9

**Extrait** : XtraWork/Repositories/XtraWorkContext.cs
```csharp
modelBuilder.Entity<Employee>(entity =>
{
    entity.HasOne(e => e.Title)
          .WithMany(t => t.Employees)
          .HasForeignKey(e => e.TitleId)
          .OnDelete(DeleteBehavior.Restrict);
});
```

**Questions** :
1. Expliquez la relation configuree (HasOne, WithMany) 
2. Que fait `OnDelete(DeleteBehavior.Restrict)` ? 

<br/>
<br/>

# Question 10 

**Extrait complet** : Flow d'une requête GET /api/employees

```csharp
// 1. Controller
[HttpGet]
public async Task<ActionResult<List<EmployeeResponse>>> GetAll()
{
    var response = await _employeeService.GetAll();
    return Ok(response);
}

// 2. Service
public async Task<List<EmployeeResponse>> GetAll()
{
    var employees = await _employeeRepository.GetAllAsync();
    return employees.Select(e => new EmployeeResponse
    {
        Id = e.Id,
        FirstName = e.FirstName,
        LastName = e.LastName,
        TitleDescription = e.Title.Description
    }).ToList();
}

// 3. Repository
public async Task<List<Employee>> GetAllAsync()
{
    return await _context.Employees
        .Include(e => e.Title)
        .ToListAsync();
}
```

**Question** : Tracez le flow complet de cette requête depuis le client HTTP jusqu'a la base de données et retour. Expliquez le rôle de chaque couche.

