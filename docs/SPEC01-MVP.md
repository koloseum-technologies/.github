# KOLOSEUM

## Minimum Viable Product Design Documentation

Written by Davy Kamanzi  
(Head of Technology and Community Relations, Ace Pro Sports Technologies)

# Table of Contents

# Introduction

Koloseum is an online esports platform that allows individuals to find and participate in gaming sessions at their local gaming lounge, compete in various competitions across different games for prize money, and shop for tickets and merchandise.

Gaming lounges are also able to join the platform and manage their daily operations, while they can also operate as esports clubs by managing their Player rosters and joining competitions. With the prevalence of gaming lounges set up only for casual gaming, existing esports organisations, teams or clubs should be encouraged to merge or partner with such gaming lounges and manage their competitive esports operations through the role system provided on the platform.

Koloseum staff will also use the platform to manage users, enter and review competition results, update store offerings, and more.

## Background

Ace Pro Sports Technologies Limited (previously Ace Pro Gaming) began staging FIFA video game competitions in 2016 and gathering statistical data on the competitors and their interactions with the competitions, including registration price sensitivity, optimal competition formats and demographics (i.e. age and residential area).

After forming [TEKKEN 254](https://www.tekken254.co.ke/), founder Davy Kamanzi (now Head of Technology and Community Relations at Ace Pro Sports Technologies) began a working relationship with Ace Pro Gaming in 2017, which saw the company's efforts expand into the local fighting game community.

With over 6 years of market research under their belt, Ace Pro Sports Technologies is now ready to disrupt Kenyan esports and emerge as the market leader by taking industry standards to new heights with Koloseum.

# Important context

To keep this design documentation as concise as possible, **only specifications for the desktop version of the (web) app are included**; none are provided for any responsive design that will be done during the platform's development.

While reading through this design documentation, it will be helpful to keep the following information in mind.

## Objectives

The collection of microservices listed and described in this design documentation constitute a **proof of concept**, whose intended purpose is to enable us to begin:

1. **Generating revenue**
2. **Collecting and analysing market data**

The successful implementation of this proof of concept is expected to culminate in our primary objective of **raising capital through investment**.

## Architecture

The platform will be **split into various microservices** serving different purposes, **grouped into four user categories**. Each category and its microservices are discussed in their own sections.

**Public**

- Landing
- Authentication
- Legal

**Players**

- Sessions
- KLSM
- Competitions
- Account

**Lounges**

- Branches
- Staff
- Account

**Backroom**

- Compliance
- KLSM
- Staff

### Why microservices?

To meet increased traffic needs, each microservice can be **scaled horizontally**. This means that **replicas of each** microservice **can be created to share the load of incoming requests** with the help of a load balancer, which is a special server that redirects incoming requests to particular replicas using one of various methods; one such method is round-robin, which involves cycling through each replica sequentially with each incoming request.

This form of scaling is **more flexible than vertical scaling**, which refers to upgrading resources on a single microservice and can only happen up to a certain limit. It also **eliminates a single point of failure** in that if one replica fails, the others can continue to receive requests. This **helps maintain customer satisfaction** as the platform continues running without interruption, while **savings are also made on time and money** that would be spent on server repairs and upgrades, and money that would be lost from users unable to access the platform during downtime.

With this logic in mind, it can be expected in the long run that of the four aforementioned user categories, **Player microservices will scale the furthest** as they are expected to serve a large majority of our user base, while **Backroom microservices may only need to scale as the number of Koloseum staff increases**. This information will be useful in drawing up future financial projections.

**Each microservice** in Koloseum **will be packaged as a container.** A container is a standardised unit of software that packages code and all its dependencies so that it can run consistently across different environments. This makes it easy to deploy and manage microservices, as they can be easily moved from one environment to another without any changes.

### Deployment with Kubernetes

**Kubernetes** (_koo-buh-net-eez_; also known as **K8s**) is an open-source container orchestration system for automating software deployment, management, and scaling. It allows for the efficient use of hardware resources by grouping containers into logical units called pods and scheduling them across a cluster of nodes.

Key features that make Kubernetes a suitable choice for handling Koloseum's deployment include:

- **Scalability:** Kubernetes can easily scale Koloseum's microservices horizontally to handle increased traffic, ensuring uninterrupted platform operation.
- **High availability:** If a replica fails, Kubernetes will automatically reroute requests to other replicas, maintaining customer satisfaction and preventing downtime.
- **Flexibility:** Unlike vertical scaling, Kubernetes enables horizontal scaling by creating replicas of each microservice, providing more flexibility and cost-effectiveness.
- **Load balancing:** Kubernetes uses load balancers to distribute incoming requests evenly across replicas, optimising resource utilisation and preventing bottlenecks.
- **Networking and service discovery:** Kubernetes provides advanced networking and service discovery features, allowing Koloseum's microservices to communicate seamlessly.
- **Deployment flexibility:** Kubernetes supports various deployment strategies, allowing for gradual rollouts, canary deployments, and blue/green deployments.
- **Multi-cloud support:** Kubernetes can run on various cloud providers and on-premises environments, providing flexibility, and avoiding vendor lock-in.
- **Advanced security and access control:** Kubernetes offers robust security features, including role-based access control (RBAC), network policies, and pod security policies.

While services like GitHub Pages, Cloudflare Pages, and Netlify offer simplicity and ease of use, especially for static websites and smaller projects, they may not match the scalability, customisation, and extensibility that Kubernetes provides for complex applications like Koloseum due to the following limitations:

- **Microservices architecture:** These services may not efficiently handle the complexities of microservices architectures, which are commonly used in large-scale applications like Koloseum. Kubernetes, with its built-in support for service discovery, load balancing, and orchestration, is better suited for managing the interdependencies and communication between multiple microservices.
- **Control over deployment environment:** These services offer limited control over the underlying infrastructure and deployment environment. Kubernetes provides fine-grained control over resource allocation, scaling, and networking, allowing for optimisation and customisation based on the specific requirements of the application.
- **Integration with technologies and services:** While these services offer integrations with a certain set of technologies and services, they may not be as flexible and extensible as Kubernetes, which can integrate with a wide range of tools and platforms through its extensive ecosystem of plugins and APIs.

## User model

**All individual users on the platform are registered as Players.** The `compliance` schema reflects this: every person has a Player record (`compliance.players`). Those individuals may then be **assigned roles** or **attached to other entities** as appropriate:

- **Backroom staff** are Players who have been assigned Backroom roles (e.g. via `compliance.user_roles`). Adding Backroom staff means assigning roles to an existing Player account (e.g. by Player ID or phone).
- **Lounge staff** are Players who are attached to a Lounge (e.g. via `compliance.lounge_staff`, linking a Player to a Lounge). Adding Lounge staff means linking an existing Player to the Lounge. **A Player can be linked to at most one Lounge at any given time** (so default landing and role-scoped options for the Lounge path are well-defined in players-account).
- **Lounge (company) accounts** are a separate account type (e.g. `compliance.lounges`) but use the **same authentication** as Players: **public-auth** provides a single login form with an option to authenticate as **Player** (default) or **Lounge**. Lounges are not “individuals” and do not require a Player record. The person who registers the Lounge is the Lounge account owner (Superuser for that Lounge).

## Platform currency

To enhance the user experience for Players, the platform will include the implementation and integration of a **loyalty program** and a **cash deposit system** into various areas as outlined below.

### Koloseum Experience Points

To encourage users to join and continue using the platform, its loyalty program will revolve around **Koloseum Experience Points (KXP)**.

Users will automatically receive a guaranteed daily minimum amount of KXP when they log onto the platform. Use of all services on the platform will guarantee users a minimum amount of KXP per action or task, with bonuses available for specific related accomplishments; these are outlined in detail in each microservice's section.

As an added incentive for users to join and continue using Koloseum, we plan to eventually enable users to trade KXP for tickets to Koloseum events and Koloseum merchandise, as well as other goods and services away from the platform (i.e. food deals, gym memberships, etc).

### Koloseum Credits

Users will be able to top up cash on the platform as **Koloseum Credits**, which they will be able to exchange for various goods and services.

Players will be able to use their credits to pay for various offerings across the platform, including competitions and merchandise. For each top-up, Players will also be awarded an amount of KXP proportionate to the top-up amount. Lounges will be able to use their credits to pay for various services on a rolling basis.

Once topped up, Koloseum Credits will be locked in the platform, meaning they cannot be withdrawn. This approach intends to build up healthy cash reserves for the platform.

### Additional context

The platform previously had a proposed platform currency called **Koloseum Game Tokens (KGT)**, which users would buy from KLSM to spend engaging in certain activities on the platform, such as registering for tournaments.

The idea behind KGT was a **single currency to access all platform competitions**. However, this would assume that all competitions on the platform are priced the same, which is not true as pricing each involves its own unique sets of factors to consider.

This means that different variations (or denominations, if you will) of KGT would then have to be sold at different prices, rendering them structurally the same as any other store item. Therefore, the KGT proposal was dropped in favour of selling competition registrations in KLSM, which would be paid for using Koloseum Credits.

## Markets

Previously, we intended to deploy our competitions on the platform through curated **Markets**, referring to a suite of services for each of the esports brands on our portfolio:

- **Ma Esto** (football)
- **Savanna FGC** (fighting games)
- **Keel Kash** (battle royale)

This approach was intended to make it easier to provide tailor-made services for each Market and its community, allowing the user access only to the services they're interested in most. It was also intended to streamline resource management, as more resources can be allocated to a Market experiencing higher traffic and away from a Market with lower demand.

However, we later realised that it would be much more efficient to develop a singular **Competitions** microservice instead, with both participation and administration deployed on the Player-facing side. This approach carries the following benefits:

- The workload is streamlined as the need to develop an additional Competitions microservice for Koloseum staff, just to carry out administration for a few services on the platform, is eliminated.
- Our tournament administration applications are made available to the public, potentially opening up additional opportunities for revenue generation and marketing.
- The initial benefits enjoyed from the original Markets arrangement are maintained; individual organisers can monitor the participation and registration/spectator ticket sales of their own competitions, while multiple microservices are not required.

Under this new system, each Market will exist on the platform as an individual competition organiser. The Competitions microservice is free to use, with users charged for advanced features that may interact with external providers and incur additional costs, such as sending out SMS notifications to Players participating in a tournament (e.g. check-in time, time and station for their next set, etc).

# Public

The following section will discuss all microservices available to the public:

- Landing
- Authentication
- Legal

## Landing

**App name:** `public-landing`

**Summary:** The platform's landing section that users see when they first visit. It presents marketing content for Players and Lounges, Pricing, and KLSM (store and events). Marketing pages will be finalised after the Backroom, Lounges, and Players microservices are complete so that platform functionality can be presented accurately.

**Business objectives**

- Attract prospective Players and Lounges by explaining platform value.
- Direct users to Authentication, Legal, and KLSM as needed.
- Present primary MVP offerings (Sessions for Players, Branches for Lounges) with clear links to learn more.

**Features**

- Users can view a home area that highlights Sessions (Players) and Branches (Lounges) with unique selling points and links to dedicated Players and Lounges marketing content.
- Players-focused content explains casual and competitive offerings and links to details for Sessions, Competitions, and KLSM.
- Lounges-focused content explains business-management convenience and links to details for Branches and Staff.
- Users can access Pricing and KLSM from the landing experience.

**Rules and constraints**

- None beyond platform-wide desktop-only scope for this doc.

## Authentication

**App name:** `public-auth`

**Summary:** Single authentication microservice for the platform. Users create an account or sign in via one flow: the **login form** offers an option to authenticate as **Player** (default) or **Lounge**; there is no separate Lounge authentication flow. Individuals register as **Players**; companies register as **Lounges** (separate account type, same auth). Players must be at least 13 years old, Lounge account owners at least 18 years old. Backroom and Lounge staff are existing Players who are later assigned roles or attached to a Lounge, respectively.

**Business objectives**

- Secure, verified access to the platform.
- Compliance with age and identity verification requirements.
- Support for both Player and Lounge registration with appropriate data collection.

**Features**

- **Login (unified):** Users log in using phone number and password, with two-factor authentication (e.g. SMS) at sign-in. The login form provides an option to authenticate as **Player** (default) or **Lounge**; the same public-auth service handles both. There is no separate Lounge login or authentication flow. **Post-login redirect:** Player login redirects to **`players-sessions`**; Lounge login redirects to **`lounges-branches`**.
- Users can request a password reset via email and complete the reset at the provided link.
- **Registration (unified):** New users register using phone number (with one-time passcode verification) and choose to register as **Player** (individual) or **Lounge** (company). Only these two account types exist; Backroom and Lounge staff are added by assigning roles or attaching an existing Player to a Lounge.
- Player registration collects: ID verification (national ID, alien ID or passport, Kenya only); parent/guardian details for minors (ID, phone verification, personal information, relation); personal information (names, date of birth, nationality, pseudonym, sex, optional gender identity and pronouns, optional varsity details); gaming and socials (primary platform, online ID, console ownership, social media from predefined lists). Users can optionally hide date of birth or show pronouns on profiles; sex and gender identity are not displayed and are for analytics only. Personal information (except pseudonym under stated rules) cannot be updated again for one year after registration or after a prior update.
- Lounge registration collects: company verification (business registration number, Kenya only); company information (name, registration date, trade name, website); superuser information (ID verification, personal information; superuser selectable from directors retrieved at verification); branch information (name, full address, amenities with optional evidence for CCTV and fire exit, contacts). Lounges without CCTV or fire exit are flagged to Players at Sessions check-in until evidence is provided and approved.
- After registration, users set their password and are redirected: **Players** to **`players-sessions`**, **Lounges** to **`lounges-branches`**. Default landing microservice for each path can be changed later in **players-account** (see Account).
- All users receive a guaranteed minimum KXP amount upon registering.

**Rules and constraints**

- Age gates: Players 13+, Lounge account owners 18+. Phone as primary identifier; 2FA at login. Consoles and social platforms from predefined lists (e.g. IGDB). Lounges claiming CCTV or fire exit require documentary evidence and approval before the flag is removed for Players.
- **Regional classification** for competitions uses the **address of the Lounge owning the Player's current membership**, not the Player's residential address (Player residential address is not collected).
- Personal information (including sex and gender) cannot be updated again for **one year** after registration or after a prior update. **Pseudonym** may be updated **once after registration**, then **once per year** after each update. Exceptions may be made on a case-by-case basis.
- Use of sex and gender identity data must be covered in the platform's **Privacy Policy** (data is for analytics only and not displayed on profiles).

## Legal

**App name:** `public-legal`

**Summary:** A static site where users can view the platform's **Privacy Policy** and **Code of Conduct**.

**Business objectives**

- Transparency and compliance with legal and community standards.
- Single place for policy and conduct information.

**Features**

- Users can view the Privacy Policy and Code of Conduct.

**Rules and constraints**

- None beyond platform-wide scope.

# Players

The following section will discuss all microservices available to all **Player** users, including those who also have Backroom roles or are attached to a Lounge as staff.

- Sessions
- KLSM
- Competitions
- Account

These microservices will be accessible to the general public, with users redirected to `public-auth` for operations requiring a registered account (e.g. registering for a competition, tracking orders on KLSM, etc).

## General functionality

Players have a shared shell across Player microservices: a quick check-in entry point for Sessions (when the user has a Lounge membership), access to Account, Help, Legal, and Logout, and notifications. Unauthenticated users can reach the `public-auth` (login) flow from this shell.

## Sessions

**App name:** `players-sessions`

**Summary:** Allows users to check in at any Lounge and open an active session for casual play or Lounge-scheduled activities (e.g. competitive fixtures).

**Business objectives**

- Enable Players to discover Lounges, book stations, and pay for session time.
- Support reservations and immediate check-in while limiting backlog (e.g. one reservation per day, member Lounge only).
- Support session edits, complaints, and checkout with clear Lounge confirmation and grace periods.

**Features**

- Users can search for a Lounge by name or ID and start a session there. If the Lounge has no CCTV or fire exit (or claims are unverified), the user is warned before proceeding.
- Users must select a game (from the Lounge's inventory) and a station (matching the game's equipment). For per-unit pricing (e.g. per match, per song), the user specifies the number of units. Users can check in immediately or reserve (up to 10 minutes; reservation once per day, member Lounge only). If the Lounge has luggage deposit, the user can indicate whether they have luggage to deposit; the Lounge assigns a slot on confirmation.
- Check-in requests stay Pending until the Lounge confirms; then the session becomes Active. Users can cancel a pending request; if they report issues, they can file a report to Koloseum staff. After Lounge acceptance, the user cannot cancel.
- During an active session, users can view elapsed time and current cost, station, game, and (if applicable) luggage deposit slot. Users can request a station/game change (status becomes Change Requested until Lounge approves), view members, report competitive results, view eatery (if offered), request assistance, file a complaint (against another session or the Lounge; Lounge is notified for another-session complaints only), and check out.
- On checkout, the Lounge receives a check-out request; session status becomes Checking out and the user sees total time and final cost. Once the Lounge confirms, the session ends. A 10-minute grace period applies: if the Lounge confirms within that period, the user is charged to the time of their checkout request; otherwise to the Lounge's confirmation time.
- Invited users see their own elapsed time, can leave the session (no checkout), and can report competitive results; they do not see total cost or invite controls.
- Users can browse Lounges by search or by directory (county to sub-county to Lounges) and view Lounge profile (ID, name, address, contacts, socials, consoles, games, luggage deposit). Users can view stations (filter/sort by console, number or price) and see station details (number, name, console, capacity, status Available/Occupied, online play, special controllers, pricing). Users can start a session or report a Lounge from a profile.

**Koloseum Credits**

- Users may spend Koloseum Credits to check out of a Lounge (end an active session).

**Koloseum Experience Points**

- Guaranteed minimum KXP for checking in and for checking out. Bonus KXP for each full hour spent in an active session.

**Rules and constraints**

- Reservation: once per day, member Lounge only; reservation holds for up to 10 minutes. Grace period for checkout billing as above. Lounges without verified CCTV or fire exit are flagged to users at check-in.

## KLSM

**App name:** `players-commerce`

**Summary:** Allows users to trade used games and consoles with other users, including both Players and Lounges. Planned to become a single point of commerce for merchandise and tickets in future updates.

**Business objectives**

- Enable a secondary market for games and consoles.
- Prepare for future expansion into merchandise and event tickets.

**Features**

- Users can trade used games and consoles with other users (Players and Lounges).
- Future: users will be able to buy merchandise and tickets via KLSM.

**Rules and constraints**

- None specified here; see `commerce` schema and implementation for transaction and listing rules.

## Competitions

**App name:** `players-competitions`

**Summary:** The Competitions microservice allows Players to discover and participate in **Leagues**, **Challenges**, and **Locals**, register as **competition organisers**, administer their own Leagues, Challenges, or Locals, and **donate to prize pools** for a tournament or Challenges edition. The microservice is **free to use**, with **charges for advanced features** that interact with external providers (e.g. SMS notifications to Players). Each **Market** (Ma Esto, Savanna FGC, Keel Kash) exists on the platform as an **individual competition organiser**.

**Business objectives**

- Grow engagement through structured competition (Leagues, Challenges, Locals).
- Enable community organisers and Markets to create and run their own competitions.
- Increase prize pools and visibility through donor-funded prize pools with caps.
- Keep core use free; monetise advanced features (e.g. SMS).

**Features**

- Users can discover and participate in **Leagues** (e.g. tiers, divisions, seasons, tournaments), **Challenges** (e.g. tiers, divisions, editions, phases, rankings), and **Locals** (single tournaments at a Lounge branch). Events belong to exactly one of: a League tournament, a Locals tournament, or a Challenges edition.
- Users can **register as competition organisers** (compliance.competition_organisers) and **administer their own** Leagues, Challenges, or Locals: create and configure them, and manage staff via the organiser and staff linkage. Organisers can run formats, rankings, match reporting, and rules (e.g. download rules and regulations). Markets (Ma Esto, Savanna FGC, Keel Kash) exist as individual competition organisers on the platform.
- **Advanced features** that interact with external providers (e.g. SMS notifications: check-in time, time and station for next set) are chargeable; core use of the microservice is free.
- Users can **donate to the prize pool** of a single **tournament** (League or Locals tournament) or **Challenges edition**. The organiser can set a base prize, allow donations, and set a **hard cap** on the total prize pool. When the total (base + donations) reaches the cap, the system automatically rejects further donations for that tournament or edition. Example: base KES 10,000, cap KES 100,000; once donations bring the total to 100,000, no more donations are accepted.
- Prize-pool **transactions** (all donations) are **captured in the `commerce` schema** when implemented; the `competitions` schema stores only (1) whether the tournament or Challenges edition accepts donations to the prize pool, and (2) the prize-pool hard cap for automatic rejection. Implementation of `commerce.sql` must support prize-pool donation transactions; `competitions` schema is extended only with `accept_donations` and `prize_pool_cap` (for example) on the relevant entities.

**Koloseum Experience Points**

- Guaranteed minimum KXP for joining a competition (e.g. joining a League tournament) as specified per competition type.

**Rules and constraints**

- Competition structure and organiser permissions follow the `competitions` schema (leagues, challenges, locals_tournaments, events, organisers, staff). Donations are subject to the cap; `commerce` owns all transaction records. Free-to-use core; paid advanced features as defined.
- **Regional classification** of Players for competition purposes uses the **address of the Lounge owning the Player's current membership** (see Authentication: no Player residential address is collected for this).

## Account

**App name:** `players-account`

**Summary:** Single account microservice for all users with a Player record. Players see Player-specific features (settings, sessions, Credits, KXP). Users who are also Backroom staff (Players with Backroom roles assigned) see, in addition or in context, Backroom-specific features (review their information, view access, view notifications). The same app displays the appropriate features based on the user's roles.

**Business objectives**

- Central place for Player account settings, membership, and balances.
- Support for 2FA, notifications, and third-party linking.
- Allow users to set default landing microservice per path (Player path and, if attached to a Lounge, Lounge path).
- For Backroom staff: transparency over current user's access.

**Features**

- **Players:** Users can update account details (e.g. optionally hide date of birth or show pronouns on profiles), switch Lounge membership, enable two-factor authentication, enable or disable notifications (e.g. SMS, email), and link their account to third-party providers. Users can view a list of their sessions and details (stations, games, members). Users can monitor Koloseum Credits balance and transactions (prize money from Leagues and Challenges, top-ups, and optionally subscription payments). Users can monitor KXP balance and transactions and redeem KXP where the platform allows.
- **Default landing microservice (per path):** Users can set their default landing microservice for **Player path** (options: e.g. `players-sessions`, `players-competitions`, `players-commerce` (KLSM) — as configured; Ma Esto, Savanna FGC, and Keel Kash are organisers within `players-competitions`, not separate microservices). For **Lounge path**, the default is **`lounges-branches`**; only users **attached to a Lounge** can change the Lounge default, and the **available options are the Lounge microservices they have access to** given their role (e.g. Branches, Staff, Account — only those they are authorised to use). This requires that a user be linked to **at most one Lounge** at any given time (see Lounges).
- **When the user has Backroom roles (staff are Players with roles assigned):** Users can view their assigned role(s) and a read-only view of Backroom microservices and features with their access indicated.

**Koloseum Experience Points**

- Guaranteed minimum KXP for: starting a Lounge membership for the first time (not if done during registration), enabling two-factor authentication (not if done during registration), and making a Koloseum Credits top-up. Bonus KXP for maintaining a Lounge membership (e.g. an increasing multiplier for each month of membership, with bonuses for milestones, e.g. 6 months, 1 year, 2 years, 5 years, 10 years, etc).

**Rules and constraints**

- Profile display rules for DOB and pronouns as in Authentication. Subscription and payment methods as configured. Backroom-specific features are visible only to Players who have Backroom roles assigned; visibility is role-based. Default landing options for Lounge path are restricted to Lounge microservices the user can access and only apply when the user is attached to exactly one Lounge.

# Lounges

The following section will discuss all microservices available to **Lounge** accounts (company entities). Lounge **staff** are **Players** who have been attached to the Lounge (e.g. via the Staff microservice); they use the same Lounge microservices according to their assigned roles, and they retain their Player identity (i.e. `compliance.lounge_staff` links `player_id` to `lounge_id`). **A Player can be linked to at most one Lounge at any given time**; this keeps default-landing and role-scoped options well-defined when they sign in as Lounge (see players-account).

- Branches
- Staff
- Account

These microservices will only be accessible to Lounge owners and staff.

## Branches

**App name:** `lounges-branches`

**Summary:** Allows Lounge users to manage day-to-day operations of their branches: check-in and check-out, active sessions, inventory (games and stations), sales and session history, KLSM trading, and eatery (menu and orders).

**Business objectives**

- Streamline front-desk operations (check-in, station changes, check-out).
- Manage sessions for Koloseum and non-Koloseum users.
- Maintain game and station inventory and optional KLSM trading and eatery.

**Features**

- **Front Desk (Superuser, Manager, Front Desk):** Staff can view and process check-in requests (confirm to set session Active; assign luggage slot if applicable), station-change requests (confirm to set session back to Active), and check-out requests (confirm to end session and set station Available).
- **Sessions (Superuser, Manager, Front Desk):** Staff can view pending and active sessions (ID, player, station, luggage slot, elapsed time, cost), start pending sessions (with player verification) and end active sessions (with payment verification; competitive fixtures auto-verified on end). Staff can start sessions for non-Koloseum users (collect name, phone, email then select game and station; optional luggage). Staff can search and view session history.
- **Inventory (Superuser, Manager, Inventory):** Staff can add, delete, and update games and their consoles/quantities (with optional equipment flags). Staff can add, delete, and update stations (number, name, console, capacity, equipment flags, pricing per hour/day/custom unit). Staff can trade used games and consoles on the KLSM market (capability; detailed flows omitted from this high-level spec).
- **Eatery (Superuser, Manager, Kitchen; for Lounges that serve food and drink):** Staff can manage orders (filter by status Pending, Acknowledged, Fulfilled, Cancelled; filter by user ID; view order items and cancellation reasons). Staff can manage menu items (Food, Drinks, Snacks) with categories, flavours, and sizes/prices. Orders update in real time as Players order during active sessions.

**Rules and constraints**

- Role-based access as above. Station status (Available/Occupied) follows session lifecycle. Maximum stations per Lounge may be limited by subscription.

## Staff

**App name:** `lounges-staff`

**Summary:** Allows the Lounge owner to attach **Players** to the Lounge as staff and assign default or custom roles. Lounge staff are not a separate account type—they are existing Players who are linked to the Lounge (e.g. via `compliance.lounge_staff`). Access: Superuser, Manager, Human Resources.

**Business objectives**

- Delegate operations without sharing the owner account.
- Support granular permissions (default and custom roles) and branch assignment.

**Features**

- Users who register a Lounge account are assigned the **Superuser** role (unlimited access; belongs only to the account creator; not transferable except at owner request). Platform provides default roles: **Manager** (all microservices except full account management and assigning Manager), **Branch Manager**, **Front Desk** (Requests, Sessions), **Inventory** (Inventory, Stations), **Kitchen** (Eatery), **Human Resources** (all Staff microservice).
- Users can create **custom roles** by selecting which Lounge microservices and services each role can access. Users can assign staff to specific branches.
- Users can add staff by linking **existing Players** to the Lounge (e.g. via a shareable invite link that leads to a form where the person signs in or registers as a Player, then is attached to the Lounge). Staff details collected may include names, date of birth, sex, optional gender identity and pronouns, nationality, address, phone, email. Sex and gender identity are not shown to Lounge owners (analytics only); pronouns can be shown if the staff member opts in. Users can view staff details (as Players attached to the Lounge), edit roles (with platform disclaimer), and remove staff (with disclaimer).

**Rules and constraints**

- Superuser is permanent for the creator. Manager cannot manage Lounge account (subscriptions, delete account) or assign Manager. Role and deletion actions require acceptance of liability disclaimer. **One Lounge per Player:** A Player can be attached to only one Lounge at any given time (enforced so that players-account can offer a single Lounge default-landing choice scoped to that Lounge and the user's role).

## Account

**App name:** `lounges-account`

**Summary:** Lounge-specific account management: company information review, branches management, and ownership transfer. **Only accessible to Lounge Superusers.**

**Business objectives**

- Support company-level and branch-level configuration by the Lounge owner.
- Enable ownership transfer of the Lounge account (Superuser transfer at owner request).
- Clarify that payment collection point is for display to Players at checkout (reference only), not for Koloseum to take a percentage of payments.

**Features**

- **Company information review:** Superusers can review and, where the platform allows, update Lounge company information (e.g. company name, trade name).
- **Branches management:** Superusers can manage branches (e.g. add or remove branches, update branch details). Payment collection point for a branch is displayed to Players at checkout for easy reference; it does not act as a middleman collecting a percentage of payments. This is made clear to users to build trust and allow accurate tracking of platform revenues.
- **Ownership transfer:** Superusers can transfer Lounge account ownership (Superuser role) to another user at the owner's request, subject to platform rules.

**Rules and constraints**

- **Only Lounge Superusers** can access this microservice. Managers and other roles do not have access; account-level and ownership actions remain with the Superuser.

# Backroom

The following section will discuss all microservices available to **Koloseum staff and representatives**:

- Compliance
- KLSM
- Staff

Backroom staff are **Players** who have been assigned Backroom roles. They use the **Players Account** microservice (`players-account`) for account and notifications; that app shows them Backroom-specific features (review information, access list, notifications) in addition to or in context with Player features.

## General functionality

Backroom users have a shared shell: navigation to all Backroom microservices, account icon (profile, Account, Help, Logout — Account is the same players-account app, which shows Backroom-specific features to staff), and notifications (recent list and full paginated list in chronological order). Implementation details (layout, icons) are left to implementation.

## Compliance

**App name:** `backroom-compliance`

**Summary:** Allows staff to manage compliance and regulatory requirements for Players and Lounges: review registrations, track documents, approve or reject data updates and amenity evidence, grant services, manage Koloseum Credits and subscriptions, and review reports and issue sanctions or bans.

**Business objectives**

- Ensure user and Lounge verification and document validity.
- Support data-update and misconduct workflows with clear approval and sanction rules.
- Enable service grants and subscription management for Lounges.

**Features**

- **Dashboard:** Staff can view high-level platform statistics (e.g. total registered users, monthly active users, users online).
- **Players:** Staff can search Players (by ID, name, pseudonym) and view profile (IDs, name, DOB, age, pseudonym, pronouns, join date, varsity if provided), documents (e.g. verified ID with type, number, country, issue/expiry, biometric confirmation). Staff can review data-update requests (identity fields) and approve or reject (reason required for reject); view update history. Staff can review misconduct reports (category, microservice, date, description, attachments, links) and sanctions (category, status Active/Expired, date); issue new sanctions (reason, optional Lounge/competition, optional KXP deduction). Lounge bans issued from Compliance must be approved on the Lounge side by a Superuser or Manager.
- **Lounges:** Staff can search Lounges (by ID, phone) and grant services (service type, duration, include/exclude list). Per Lounge: profile (IDs, company details, registration dates), Branches (list and per-branch profile and amenities with Pending/Approved/Rejected and evidence), Staff list (name, branch, role, link to Player page), Documents (e.g. company registration). Staff can review Lounge data-update requests and history, Koloseum Credits (transactions, transfers, subscriptions; grant service), and misconduct (reports and bans; issue bans with reason and optional branch/competition). Per branch, staff can view profile and amenities (type, status, evidence, rejection reason if any).

**Rules and constraints**

- **Reports** use categories: Nuisance, Discrimination, Harassment, Criminal Activity. **Sanctions (Players):** Platform, Lounge, Competition, KXP. **Bans (Lounges):** Platform, Branch, Competition.
- **Data-update requests:** Player identity fields (first, middle, last name; date of birth; nationality; pseudonym); Lounge identity fields (company name; trade name).
- **Limits:** Report/misconduct description max 1,500 characters; attached files max 30MB total; external evidence links max 5; sanction/ban reason max 500 characters; amenity rejection reason max 500 characters.
- Lounge bans issued from Compliance must be approved on the Lounge side by a Superuser or Manager.

### Future work

Future updates should allow staff to monitor compliance status of Players and Lounges with relevant authorities (e.g. Sports Registrar, esports federations and associations).

## KLSM

**App name:** `backroom-commerce`

**Summary:** Allows staff to manage store inventory, track payments and orders, manage game and console trades between users, and monitor sales. Access: Superuser and Commerce users.

**Business objectives**

- Oversee and support the KLSM marketplace and store.
- Align with `commerce` schema for listings, orders, and transactions.

**Features**

- Staff with Commerce role can manage store inventory, track payments and orders, manage game and console trades between users, and monitor sales (scope and operations to align with `commerce` schema and Backroom Commerce role).

**Rules and constraints**

- Access restricted to Superuser and Commerce.

## Staff

**App name:** `backroom-staff`

**Summary:** Allows authorised users to assign and manage Backroom roles for **Players**. Backroom staff are not a separate account type—they are Players who have been assigned Backroom roles. Access: Superuser and Human Resources users.

**Business objectives**

- Control which Players have Backroom access and which roles they have.
- Support future expansion into a full HR tool.

**Features**

- **Roles:** Platform provides Superuser (all microservices; only for the three founding directors: Fanuel Opiyo, Davy Kamanzi, Daniel Juma; cannot be assigned by anyone), Compliance (Players, Lounges), Competitions (single microservice; access may be scoped by Market: Ma Esto, Savanna FGC, Keel Kash), Media (_The Focus_), Commerce (Storefront, Warehouse), Human Resources (Staff).
- Staff can view a table of Backroom staff (Players who have Backroom roles; name, roles, registration date) and **add staff by assigning roles to an existing Player** (lookup by Player ID or phone number; invite/confirmation as implemented). Per staff member: view profile (picture, name, DOB, age, pronouns, registration date, roles) and edit roles or revoke access (with liability disclaimer).

**Rules and constraints**

- Superuser assignment restricted as above. Role changes and revoke require acknowledgment of disclaimer.
