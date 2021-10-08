# Cloud Notes
> The ultimate goal of a cloud architecture is to meet the requirements of the business case. Every decision needs to be
  made with this in mind.
## Parts of a Cloud
### General Categories
- Major services:
  - Management
    - Minor services: operations, logging
  - Security
    - Minor services: Identity Access Management (IAM), encryption
  - Governance
    - Minor services: service governance, cost governance
  - Database
    - Minor services: big data, transactional
  - Storage
    - Minor services: block, object, file
  - Compute
    - Minor services: platform, High Performance Computing (HPC)

### Cloud Storage
There are three types of cloud physical storage:
1. File storage. Traditional method of storing data - a single piece of complete data is stored in one location with 
   one path to that data. It's easy to interface with and understand, but retrieval can be slow and data can be stored
   sub-optimally. Note that file storage can also sit on top of block or object storage, behaving more as an interface.
2. Block storage. Large files are split into blocks and stored in the most efficient way possible. Has filesystem-like 
   behavior and thus is easy to mount and interface with a traditional filesystem frontend. Data can be distributed and
   retrieval can be faster because it can be done in parallel to assemble the original file. However, block storage
   can be expensive, and because it has limited support for metadata, it typically must be dealt with in the application
   or database level.
3. Object storage. Each piece of data is wrapped in an object, as well as metadata and a unique identifier. Each object 
   storage volume acts as its own self-contained unit. Has great support for metadata, and scales out cheaply and easily.
   However, interacting with an object storage volume with a traditional filesystem can have performance issues, as the 
   data isn't structured in a way that traditional filesystems can interact with easily. Partial data can't be modified -
   the entire object must be written to at once to update.

## Concepts
- Edge Computing: taking data workloads and performing them at the nearest level, versus sending data back to some
  centralized system and processed there. Performing computations closer to the source of the data and helps combat
  bandwidth limitations, latency issues, network disruptions, etc that come with moving around huge amounts of data.
- Meta- and micro-architecture:
  - Micro: a functional primitive for a particular piece of architecture - storage, processing, security, governance, 
    etc.
  - Meta: how all the component parts work and play together to form the ultimate cloud solution.

## Design Decisions
- Don't forget that there are decisions outside of software that go into a cloud-based system: security, governance,
  conforming to regulations (in certain sectors), etc.
  - Governance relates to how costs, privacy, and security of a cloud platform are managed by a company. With 
    traditional on-site solutions it's typically not hard to determine current and future costs, and the applications
    developed by the departments using the platform are easy to keep track of. With IaaS cloud platforms this 
    information is a bit more difficult to keep controls in place to prevent runaway operational costs and to ensure all
    apps can communicate well with one another.
  - Cloud Management tools provide a rich set of capabilities for managing hybrid cloud environments - a possible mix of
    public and private clouds, virtual and bare-metal servers, edge computing, etc.
  - Cloud lifecycle management: manages the dynamic nature of a cloud environment, accelerates provisioning, 
    facilitates flexibility, and helps rapidly meet business needs. This typically follows a cyclical pattern of:
    Plan -> Setup -> Build -> Test -> Deploy -> Monitor -> Manage -> Meter & Charge -> Optimize -> Plan, and so on...
  - Cloud broker: a third party company that adds value to cloud services on behalf of cloud service consumers. 
    Additionally, many cloud providers offer broker services that allow their platform to communicate with external
    providers, making multicloud setups smoother to implement and operate. 
- Disaster recovery considerations:
  - Active/Active: storage systems that are always functioning and therefore always redundant.
  - Active/Passive: some are active but some are passive, only being spun up when needed.

## Cost Savings
- Hard costs: operational savings achieved by leveraging cloud-based architecture. Clouds allow processing to be done
  for less money (typically).
  - Existing costs: buying servers, buying software licenses, running datacenters, paying people to maintain said
    centers. These are *capital expenditures* (CapEx).
  - Cloud costs: eliminates most CapEx costs but typically incurs more *operational expenditures* (OpEx).
- Soft costs: harder to define, but include things such as time to market and agility in meeting business needs. Soft
  costs tend to have more of an impact on a business.