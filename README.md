# PROJET 1 - XtraWork (CODE FIRST)

<br/>
<br/>



# **PARTIE 0 — INSTRUCTIONS**

- **But :** Préparer l’environnement et comprendre la structure du projet.
- **Contenu :** Installation de .NET + SQL Server, configuration du projet, conventions de nommage et architecture en couches (Entities / Repositories / Services / Controllers).
- **Résultat :** Environnement prêt pour le développement de l’API.

### DOCUMENTATION SUR HTTP://INSKILLFLOW.COM

<img width="940" height="653" alt="image" src="https://github.com/user-attachments/assets/c70a31bb-2828-4b05-9bfb-ac4d2f917adb" />
<br/>
<img width="1132" height="649" alt="image" src="https://github.com/user-attachments/assets/38738f61-196b-4c9e-ac6a-52b8d54c14a5" />
<br/>
<br/>


# **PARTIE 1 — BASE DE L’API**

**But :** Construire la première version fonctionnelle de la Web API.
**Contenu :**

* Création du projet **XtraWork**
* Implémentation des entités `Title` et `Employee`
* Configuration d’**Entity Framework Core**
* Couches : Repository → Service → Controller
* Tests CRUD via **Swagger**
  **Résultat :** API locale pleinement fonctionnelle (lecture, ajout, modification, suppression).


# Liens

> - **THÉORIE-01** — [01 · Code First : Théorie, concepts ASP.NET Core et architecture](https://www.inskillflow.com/courses/take/asp-net-copilot-1/texts/68852637-01-codefirst-theorie-concepts-asp-net-core-et-architecture)
> - **TUTORIAL-01** — [02 · Code First : Pratique 1 – Créer une API complète (ASP.NET Core + EF + SQL Server)](https://www.inskillflow.com/courses/take/asp-net-copilot-1/texts/68852025-02-codefirst-pratique-1-creer-une-api-web-complete-avec-asp-net-core-entity-framework-et-sql-server-pour-gerer-des-employes-et-leurs-titres-de-poste)
> - **Medium** — [Building Web API using ASP.NET Core (for dummies)](https://juldhais.net/building-web-api-using-asp-net-core-for-dummies-3e0c59881432)
> - **GitHub – CODE 1** — [ASPNETCORE-WEB-API-XtraWork-PART-1](https://github.com/inskillflow/ASPNETCORE-WEB-API-XtraWork-PART-1)



<br/>

# **PARTIE 2 — FONCTIONNALITÉS AVANCÉES**

**But :** Étendre et sécuriser l’API pour un usage professionnel.
**Contenu :**

* **Validation** : mise en place de **FluentValidation**
* **Sécurité** : authentification **JWT**, rôles et autorisations
* **Monitoring** : **Serilog**, health checks, cache
* **Optimisation** : pagination, performance EF, déploiement production
  **Résultat :** API sécurisée, performante, prête pour la mise en production.

# Liens

> - **PART-1** — https://github.com/inskillflow/ASPNETCORE-WEB-API-XtraWork-PART-1
> - **PART-2** — https://github.com/inskillflow/ASPNETCORE-WEB-API-XtraWork-PART-2
> - **TUTORIAL-02** — [03 · Code First : Pratique 2 – Fonctionnalités avancées](https://www.inskillflow.com/courses/take/asp-net-copilot-1/texts/68857888-03-codefirst-pratique-2-api-web-asp-net-core-partie-2-fonctionnalites-avancees)



<br/>


# **PARTIE 3 — TESTS DES ROLES ET ENDPOINTS**

**But :** Vérifier le bon fonctionnement complet de la sécurité et des routes.
**Contenu :**

* Tests unitaires et d’intégration sur les **endpoints protégés**
* Vérification des **droits d’accès** selon le rôle (`User`, `Manager`, `Admin`)
* Scénarios complets : création, mise à jour, suppression et accès restreint
* Validation du comportement JWT dans Swagger et via requêtes API
  **Résultat :** Garantie que les rôles, permissions et sécurités fonctionnent correctement sur tous les endpoints.

# Liens
> - **GUIDE DE TEST 1** — [Important – Guide de test (Partie 2 du projet XtraWork)](https://www.inskillflow.com/courses/take/asp-net-copilot-1/downloads/69073668-important-guide-de-test-instructions-et-tests-pour-la-partie-2-du-projet-xtrawork-avec-l-approche-codefirst)
> - **GUIDE DE TEST 2** — [Google Doc – Guide de test](https://docs.google.com/document/d/1e8ILXdDQAA7qKsWbLVYVclfQ4rDHggeq/edit)
> - **PART-3 – TESTS** — https://github.com/inskillflow/ASPNETCORE-WEB-API-XtraWork-PART-3-TESTS



<br/>


# **PARTIE 4 — EXAMEN FINAL**

**But :** Évaluer la maîtrise complète du projet.
**Contenu :** Réalisation d’une API complète et sécurisée, incluant validation, sécurité, tests, pagination, logs et documentation Swagger.
**Résultat :** Livraison d’une API **production-ready**, démontrant la compréhension de tout le cycle de développement ASP.NET Core.

# Liens
> - **PART-4 – EXAMEN** — https://github.com/inskillflow/ASPNETCORE-WEB-API-XtraWork-PART-4-EXAMEN
> - **EXAMEN** — [Énoncé – 21-EXAMEN-EDUTRACK.md](https://github.com/inskillflow/ASPNETCORE-WEB-API-XtraWork-PART-2/blob/main/XtraWork/21-EXAMEN-EDUTRACK.md)
