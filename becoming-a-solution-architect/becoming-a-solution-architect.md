# Becoming a Solution Architect

## Table of Contents
### [The Architect and the Team](#the-architect-and-the-team)
- [Software Team Rules](#software-team-roles)
- [The Functional Analyst](#the-functional-analyst)
- [The Lead Developer](#the-lead-developer)
- [The Solution Architect](#the-solution-architect)
### [What Makes a Great Architect](#what-makes-a-great-architect)
- [The Architect Job Description](#the-architect-job-description)
- [Responsibilities of Great Architects](#responsibilities-of-great-architects)
- [Personality Traits of Great Architects](#personality-traits-of-great-architects)
- [Common Pitfalls of Architects](#common-pitfalls-of-architects)
### [Designing a Software Architecture](#designing-a-software-architecture)
- [What is a Software Architecture?](#what-is-a-software-architecture)
- [Key Principles of Software Architectures](#key-principles-of-software-architectures)
- [Guidelines for Software Architectures](#guidelines-for-software-architectures)


---

## The Architect and the Team
### Software Team Roles
- Principles of software development - every company does some things differently, but there is always a need to:
    - *Understand the business problem*, to which a software solution must be created. Someone has the responsibility of clarifying
      these problems.
    - *Document non-technical business solution* - discussing functionality in a non-technical way.
    - *Convert solution to technical architecture* at a very high level, i.e. diagrams, etc. Nothing is specified in code yet.
    - *Convert architecture to code*, i.e. the developers.
    - *Manage developers*.
    - *Test code*.
    - *Deploy code*, ensuring software meets requirements.
- Team roles
    - User Representatives - people who will be using the software. There will be many end-users, so one or two of them will be
      elected to represent the userbase as a whole.
    - Functional Analyst - gathers requirements from the user representatives and creates functional specifications of what needs
      to be built. This specification is high level and abstract.
    - Solution architect creates a high level, non-technical description of the system as a whole. Think diagrams, etc.
    - Lead developer takes solution document, and creates the actual software implementation.
        - Lead dev is responsible for *all* implementation, as well as assigning tasks to the team of developers.
    - QA is responsible for testing the product, and thus needs to know how the software is supposed to behave. Responsible for
      implementing tests & continuously running them.
    - Deployment manager deploys the code - nightly builds, staging builds for review, pushing to production, etc. This is the only
      role in the team that can push the code to the production environment, and is completely separate from the developers.
    - Project manager is responsible for planning the project - milestones, deadlines, etc.
    - Trainer trains the rest of the team (though many companies do not have anyone fulfilling this role).
- Required skills for every member of the team
    - Understanding the business.
    - Cross-domain understanding - each member knows a little bit about what other roles do.
    - Multiple perspectives - seeing a problem from another point of view. I.e. the solution architect is looking at a problem from
      a high level view while the lead developer is looking at the same problem from a technical point of view.
    - People skills - good communication.
    - Lifelong learning.

### The Functional Analyst
  - Responsibilities
    - All functionality - if there is some functionality that is wrong or missing when the project is deployed, the blame falls on
      the functional analyst
    - Capturing functional requirements, and continuously revalidates the requirements already gathered.
      - Different representatives may request conflicting features, and it's the functional analyst's responsibility to resolve
        those.
    - Constantly ask questions - *What do you mean? How does this fit with...?*
    - Resolving conflicts within the team of representatives to get consistent requirements that everyone is happy with.
    - Producing a Requirements Specification - complete and non-technical specification of the functionality the software solution
      must provide.
  - Required skills
    - Conflict management/resolution to reach workable compromise.
    - Communication skills: communicate the specifications to pretty much everyone else on the team so everyone is in total
      agreement on what the software must do once it's completed. It's important that the *entire team* is on board with the
      requirements before the technical solution is implemented.
    - Attention to detail - ensuring there are no conflicting or unreasonable requirements made, and that all requirements are
      discovered.
    - Precise communications - know what level of description is required when talking to people.
    - Know when detail is necessary and when it's not
    - Adept at dealing with differing opinions
    - Great relationship skills
    - Good listener
    - Can create clear and precise documents
    - Skilled in Office tools
  - Pros and cons of this role
    - Pros
      - Key role
      - Lots of interactions - connected with pretty much everyone involved in the project
    - Cons
      - May have to work with bad user reps
      - Will usually be exposed to conflict
      - Gets the blame when requirements are missing

### The Lead Developer
- Formal or informal leader of the development team
- Connects developers to the solution architect
- Takes the architecture design of the solution from the solution architecture and creates a technical, code-level implementation
  of it
- Responsibilities
  - The primary responsibility is to lead and mentor the other developers
  - Identifies coding tasks, splits them up, and assigns them to the development team
  - Making sure every member is productive and successful - assigning the easy tasks to junior devs and complex ones to senior
    devs, etc.
  - Conducting code reviews - reviewing all the code that is being written
  - Mentor developers, provide learning resources
- Skills
  - Mentoring - know who is junior and know who is senior and assign tasks accordingly
  - Programming. Though not every lead developer will be writing code, they will be reviewing code, so their code skills must be
    top-notch.
  - Values-driven; understands that elegant, reusable, efficient code are important and strives to incorporate them into the
    codebase.
  - Has a good relationship with the solution architect
  - Adept at creating technical specifications, as these describe the tasks that must be done by the development team.
  - Adept as build & configuration management. Though the Deployment Manager is technically in charge of this, a lot of the time
    the build system is created by the lead developer at the onset and handed to the DM later
  - Adept at debugging, log inspection, etc.
  - Creates own tools as needed
- Pros and Cons
  - Pros
    - Lead-in to solution architect
    - Still involves coding; is typically the highest level you can go and still be a developer
    - Gets to choose what tasks to work on
  - Cons
    - Stuck between SA and devs; if devs can't implement the architecture choices made by the SA, the lead dev is expected to
      resolve those conflicts
    - Lifelong learning required; need to have a *ton* of technical knowledge.
    - If project management is poor, may just turn into a normal dev position; ex. if deadlines are too tight then there is no time
      for the lead dev to mentor and coach the other devs, and instead is focusing or normal development
    - Vulnerable to offshoring

### The Solution Architect
- Takes functional specs from the Functional Analyst and turn them into a technical specification
  - Technical but no actual implementation details included
- Maintains a working relationship with the lead developer
- Stays at the overview level of the project at all times - never going down to the implementation detail of a certain part of
  the design
- Responsibilities
  - *All* the technical aspects of the project
  - Functional Requirements -> Technical Architecture
  - Balancing design patterns, requirements, code elegance, concepts
  - Researching key technologies
  - Has a deep understanding of design patterns - a large portion of architecture is using software design patterns
  - Motivates and guides the team
  - Ensures the lead developer is successful
- Skills
  - Understanding functional requirements
  - Some technical knowledge (pattern pros and cons, etc)
  - Leadership skills
  - Fluent in UML & design tools
  - Experience with code generators (which translate architecture to actual code)
- Pros and cons
  - Pros
    - The most valuable member of a team & tends to get paid as much
    - Very visible - communicating with everyone involved in the project
    - Safe from outsourcing as it involves a lot of leadership and direct communication
  - Cons
    - Difficult to stay up to date
    - Must maintain a broad level of technical knowledge
    - May get bad requirements - these are difficult to identify since the SA may not know what the business' requirements are.
    - SA handles a lot of responsibility - the SA is the first to blame if the system is incorrect or designed poorly

## What Makes a Great Architect

### The Architect Job Description
- Main responsibilities expected by employers
  - Collaborate with the team
  - Use tools & methodologies
  - Develop high-level specifications

### Responsibilities of Great Architects
- *Insight*
  - Abstracting complexity - taking complicated requirements and turing them into a manageable model. The model describes the
    essence of a system by exposing important details and significant constraints
      - The simplest possible implementation of a solution that incorporates all functional requirements and constraints.
  - Understanding tradeoffs - making critical decisions in terms of implementation, operations, and maintenance. Often times URs
    want everything - good performance, cheap, fast - requirements that tend to conflict with one another. These decisions result
    in tradeoffs that must be well-documented and understood by others.
- *Leadership*
  - Maintain control - architecture changes over the lifetime of a project, and the implementation may begin to deviate from the
    architecture. This requires constant monitoring of the implementation.
  - Stay on course - keeps the long-term vision always in mind. When confronted with scope creep (when the goals of the project
    begin to expand after the project has already begun), the architect needs to know which requests to keep and which to deny -
    when to reject some requests so that other requests can be approved.
- *Communication*
    - Explaining the benefits of the architecture - the SA works with executives to sell them on the architecture that they've
      designed. They must deliver results that impact the bottom line.
        - Need to know the goals of the business and how the architecture satisfies those goals.
    - Inspire stakeholders - people like the functional analyst, lead developer, developers, user representatives, executives, etc
      are all stakeholders. The SA must mentor stakeholders and ensure that they understand the software architecture.
- *Vision*
  - Focusing on the big picture - never going too into detail. Must always keep in mind the landscape of the entire project at all
    times. This encompasses *every* part of the system, from the software to the platform its on, to its deployment, etc.
  - Acting as an agent of change - inspiring change in organizations that are unprepared for the software development process.
    Some roles might not be clearly separated or filled at all, executives don't understand the importance of certain roles, etc.


### Personality Traits of Great Architects
- *Steadfast*
  - Things change all the time in IT; being able to adapt quickly as a project changes
- *Trustworthy*
  - Dev team and executives must trust that your architecture will get the job done and satisfy the requirements. Having previous
    successful projects builds trust, as well as having credentials for a variety of skills.
- *Persuasive*
  - The SA must sell their architecture to the stakeholders. Principled negotiation is useful for achieving mutual cooperation with
    the stakeholders.
  - When asked to create a system that is better, faster, and cheaper, the SA must convince the stakeholders which two of the three
    are  the cheapest and most sensible.
- *Confident*
  - The SA must have confidence in their architecture, at the very least on the outside.

### Common Pitfalls for Architects
- Types of power:
  - *Implicit power*: comes with the job.
  - *Granted power*: power given to an architect, for example an architect might have power over developers if they often come to
    you for help - the team has granted you power.
  - *Personal power*: ability to get back on your feet quickly when encountering a setback.

## Designing a Software Architecture

### What is a Software Architecture
- A solution meets all documented requirements (business, speed, scalability, etc) while optimizing for certain qualities, such
  as performance, security, and manageability.
- Requirements
  - *User Requirements*: what the end-users are looking for in terms of functionality.
  - *Business Requirements*: what's important to the business - do they want to be cheaper than the competition? Faster?
  - *IT System Requirements*: technical requirements that are dictated by the IT system; the app may be cloud-based, installed
    on specific hardware, etc.
- What composes an architecture?
  - The structural elements (typically objects) and interfaces composing a system - the foundational aspects of the solution.
  - How these elements interact with one another.
  - How these elements compose to form larger structures - the high-level data structures.
  - The architectural style/pattern that describes the architecture.
  - The functionality, usability, resilience, performance, reuse, etc of a solution.
- Goals of a software architecture
  - Documents the high-level structure of the project.
  - Does not go into implementation details.
  - Minimizes complexity.
  - Addresses *all* requirements.
  - Is compatible with all use cases and scenarios.
- Design tips
  - Build solutions to change, not to last - architectures may change frequently. Entire submodules may come and go, and the
    ability to remove them and replace them quickly and easily is vital.
  - Use modeling, but only for the purposes of analysis and risk-management; avoid generating code, automatically or otherwise -
    this is the job of the lead developer.
  - Use visualizations to communicate and collaborate, communicate via descriptive diagrams to communicate with stakeholders.
  - Identify and research critical points of failure; ensure that your solution will address these concerns.

### Key Principles of Software Architectures
*The goal of a software architect is to minimize complexity.*
- This can be accomplished by separating the design into separate areas of concern.
  - In a layered architecture, each area of concern is typically given its own layer.
  - The GUI may be its own layer, data retrieval be another, etc.
- Key principles
  - Separation of concerns (taking to a database, presentation, etc) into their own areas so that the responsibility of code is
    separated logically.
  - Single responsibility principle: the elements in a design have one and only one responsibility. A component should only have
    responsibility towards its own bubble of concern.
  - Principle of least knowledge: modules do not have any knowledge of the internals of other modules; communication should be
    based on interfaces.
  - Don't repeat yourself; do not have multiple components that all have the same responsibility present in a system.
  - Minimize upfront design - the initial design should be as short and brief as possible so that coding can begin immediately.
    - Prevent the antipattern of spending months on a design upfront without accomplishing anything code-wise.
    - Identify the minimum completeness of a system that needs to be fleshed out for coding to begin and aim for that.
      - The rest of the system can be designed while the dev team works on the code.

### Guidelines for Software Architectures
- Overall guidelines to follow, regardless of the design pattern used:
  - Use consistent patterns in each layer of concern.
    - Ex. the GUI layer may use the MVC pattern, so *every* component must be designed using the MVC pattern - don't mix and match.
  - Do not duplicate functionality. Don't recreate or re-instantiate components in different layers, rather instantiate only one,
    and have it work in tandem with each layer that requires it.
  - Prefer composition over inheritance - it's better to use composition when given the choice, as it doesn't tie the architecture
    to a specific inheritance hierarchy that has to be designed and maintained.
    - Typically these guidelines are co-created with the lead developer, if possible.
  - Establish a code convention - naming conventions, namespacing, etc.
    - Typically these guidelines are co-created with the lead developer, if possible.
- Layered Pattern guidelines
  - Separate areas of concern.
  - Clearly define communication between layers, and do not expose these mechanisms to higher layers.
    - If a layer is replaced with something different, it should not impact any of the higher layers
  - Use abstraction to loosely couple layers - hide as many internal details as possible from other layers.
  - Don't mix different types of components in a layer, i.e. data components should not be present in the presentation layer.
  - Use a consistent data format within a layer.
- Component Pattern guidelines
  - No component should rely on the internals of another; public interfaces should be the only means of communication.
  - Do not mix roles in a single component. One component should not display itself on a GUI and also fetch its own information
    from a database, for example.
  - Define clear contracts for components.
  - Abstract system wide components away from other layers.

