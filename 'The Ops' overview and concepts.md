**Note - this notes page relates to all notes in this repo BUT this page in particular ties in tightly with the "CICD & workflow orchestration" page **


## What is "The Ops"?
"The Ops" is a term that's emerged in response to the evolution of traditional DevOps principles. It reflects how DevOps engineers often have to adapt the classic DevOps automation and collaboration practices to automate processes in adjacent domains. Traditionally, it is composed of three domains:

- **Dev (development)-Ops** – The original pillar, focusing on automating and managing the entire software development lifecycle—from code to production—with continuous integration and delivery (CI/CD), infrastructure as code (IaC), and monitoring.
    
- **Data-Ops** – Applies DevOps principles to data engineering and analytics. This includes automating data pipelines (ETL or ETLv: extract, transform, load, and optionally visualize), ensuring data quality, versioning, and collaboration across teams.
    
- **ML (machine learning)-Ops** – Focuses on the automation and governance of machine learning lifecycle tasks, such as model training, validation, deployment, monitoring, and versioning—ensuring reliable and scalable ML/AI systems in production.

---

## The Golden "Ops" Principle

**"All complex, multi-stage, and multi-department workflows can be automated."**
This is my personal golden rule. 

Just as DevOps was originally created to automate every stage of the software development lifecycle—bridging once-siloed domains like development, testing, and operations—we can apply similar principles across other disciplines.

By leveraging established DevOps tools, methodologies, or even custom-built automation, any workflow that involves an element of "development" can be streamlined. Here, _development_ is broadly defined as _"creating something through code."_ This includes not only software, but also datasets, machine learning models, reports, simulations, and more.




---

**Lets explore each of the traditional "ops" domains in depth, learning principles, terms, and tools that apply to each domain.**

---


# section 1 ) DevOps

## **History and Foundational Principles**

DevOps was incepted at the 2008 Agile Conference during a panel on applying Agile management techniques to infrastructure. In 2009, the Velocity Conference hosted a talk titled “10 Deploys a Day: Dev and Ops Cooperation at Flickr”, which made waves in the tech community. Shortly after, the term DevOps was coined—and the movement began to gain serious traction.

DevOps is rooted in three foundational principles, often referred to as “The Three Ways”, as defined in the seminal text _The DevOps Handbook_:

###### 1) **The Principle of Flow**
This principle is all about creating a seamless flow from development to operations to the customer. To achieve this, we:

- **Make work visible** – through tools like GitHub, GitLab, etc.
- **Reduce the size and duration of work intervals** – by merging frequently and deploying often
- **Build in quality** – using automated testing and static analysis
- **Continuously improve** – by optimizing for speed, clarity, and feedback

When flow is implemented effectively, teams can run CI/CD pipelines, spin up infrastructure on demand, and roll out application changes smoothly. In essence, you're operating in a "flow state".

###### 2) **The Principle of Feedback**
Feedback is critical for catching problems early, improving team coordination, and securing systems. Every organization implements DevOps differently, and feedback is how you fine-tune your organizations DevOps implementation.

Strong feedback loops:
- Help surface issues before they impact customers
- Allow you to adjust your DevOps strategy based on how your team actually works
- Make security a proactive part of the delivery cycle (not an afterthought)


###### 3) **The Principle of Continual Learning**
This principle is about staying open to new ideas, technologies, and approaches.

New tools and methods can:
- Improve speed and reliability
- Enhance security
- Reduce friction in workflows


---

## **DevOps Is a Concept, Not any one thing**

Before we get technical, it’s important to remember that **DevOps is a concept**, not a specific tool or platform. It’s best summarized as:

> “Improving the efficiency and reliability of the software delivery lifecycle.”

To support this, an ecosystem of tools and standards has emerged—but because the concept is broad, implementations can vary widely but successful implementation will normally always result in:

1. **Full automation of the SDLC** – from build to test to deploy
2. **Automated, reproducible infrastructure** – for development, testing, and production, and sometimes for compute tasks outside the direct SDLC (e.g., sandbox environments, raw dev compute)
3. **Near-perfect code quality** – via automated testing, linting, and reporting
4. **Scalable, secure, and highly available deployment platforms** - with an emphasis on de/loose coupling between services (microservices) HOWEVER microservices are not always the answer and DevOps implementations often fail when people try to use/convert to microservices because its trendy not because its necessary.
5. **Twelve-Factor App design principles** – to ensure apps are stateless, portable, and production-ready (we’ll cover this later)
    

---

## **Why We Need DevOps Engineers**
 #I_DONT_LIKE_THIS_REWRITE
Pure DevOps means automating the entire software lifecycle. To do that, you need people who understand specialized tools, have deep systems design knowledge, knowledge of DevOps specific strategies, and who have advanced troubleshooting and performance tuning capabilities, In my interviews with other DevOps engineers and my own experience I have found that a DevOps engineers job actually can be broken down into 3 different roles:

###### 1) **Development Efficiency Engineer**
Focused on improving the speed and quality of software delivery. This includes:

- Building reusable CI/CD pipelines
- Creating universal dev environments (e.g., Docker, DevContainers)
- Automating repetitive tasks and standardizing repo structures


###### 2) **Integrations Engineer**
Thanks to their deep understanding of tooling and automation, DevOps engineers help integrate those tools into developer workflows/working alongside devs to help modernize applications.

Example: GitLab uses Chef (via Omnibus) as part of its automation for installing and configuring GitLab. A DevOps engineer is the kind of person who would design and maintain that solution—bridging product and operations through automation.

###### 3) **Deployment Architect / Platform Engineer**
Responsible for designing and maintaining deployment platforms such as:

- Kubernetes clusters
- Non-orchestrated container setups
- Hybrid cloud deployments
- Serverless application backends

They also handle performance tuning, failover strategies, and infrastructure security/troubleshooting.


---

## In Summary

DevOps isn’t just about CI/CD or automation. It’s a way of thinking about how to build, deploy, and scale software reliably and efficiently. Every organization implements it differently—but the goal is (almost) always the same: #I_DONT_LIKE_THIS_REWRITE


1. **Full automation of the SDLC** 
2. **Automated, reproducible infrastructure**
3. **Near-perfect code quality**
4. **Scalable, secure, and highly available deployment platforms**
5. **Twelve-Factor App design principles**


so lets cover how we accomplish each of the above


---

## **Full automation of the SDLC** 

The backbone of DevOps is **automating the entire software development lifecycle (SDLC)**—from writing code to testing, building, and deploying it. The primary way we accomplish this is through well-structured **repositories** and their associated **CI/CD pipelines** and strategies. To start, we need to understand...
### **Repository Organization Structures**

This refers to how your code repositories are structured, and it directly impacts where pipelines run, how merges are managed, how errors are handled, and how code is reviewed and controlled.

A well-organized repository structure helps developers work faster, reduces confusion, and shortens the time-to-deploy metric. On the other hand, a poor structure can slow teams down, introduce bugs or merge conflicts, and even lead to application downtime. Lets go over some common repo organization models and their pros and cons.

#### **1) Git Flow**
**Concept:**
Git Flow is a branching model that uses multiple long-lived branches to manage the development process. It is designed to facilitate parallel development, feature integration, and release management.

**Key Branches:**
```
            +------------------+
            |      master       |
            +------------------+
                     ^
                     |
            +------------------+
            |     develop      |
            +------------------+
                     ^
         +-----------+-----------+
         |           |           |
+----------------+ +----------------+ +----------------+
| Feature Branch | | Release Branch | |  Hotfix Branch |
+----------------+ +----------------+ +----------------+
```

- **`master` or `main`:** Contains production-ready code.
- **`develop`:** Integrates features and serves as a base for feature branches.
- **Feature Branches:** Created off `develop` for developing new features.
- **Release Branches:** Created from `develop` for preparing a new release.
- **Hotfix Branches:** Created from `master` for quick fixes to production.

**Advantages:**
- Clear separation between different stages of development (features, releases, hotfixes).
- Supports parallel development and multiple releases.

**Disadvantages:**
- Can become complex and cumbersome with many branches.
- Requires discipline to manage and merge branches effectively.

#### **2) Trunk-Based Development**
**Concept:**
Trunk-Based Development focuses on having a single long-lived branch (often called `trunk`, `main`, or `master`) where all changes are integrated. Developers work in short-lived branches or directly on the trunk.

**Key Practices:**

```
+------------------+
|      trunk       |
+------------------+
        ^  ^  ^
        |  |  |
+-------+  |  +-------+
| Short-Lived Branches |
+-------+  |  +-------+
           | 
+-------+  +-------+
| Feature Flag 1 |
| Feature Flag 2 |
+-------+  +-------+
```

- **Short-Lived Branches:** Developers create short-lived branches for features or fixes, merging back into the trunk frequently (e.g., daily).
- **Continuous Integration:** Regular integration with the trunk to ensure that the code remains in a deployable state.
- **Feature Flags:** Use feature flags to control the deployment of incomplete features.

**Advantages:**
- Simplifies branch management and reduces complexity.
- Encourages continuous integration and faster delivery of changes.

**Disadvantages:**
- Requires a robust CI/CD pipeline to handle frequent merges.
- May not provide as clear a separation between development stages as Git Flow.

### **2. Multi-Repo vs. Mono-Repo for Repo Strategies**

#### **Multi-Repo Strategy**

**Concept:**
In a multi-repo strategy, each project or service is stored in its own repository. This approach allows teams to manage and version control different parts of the system independently.

**Structure:**

```
+------------------+
| Repo A (Service A) |
+------------------+
        ^
        |
+------------------+
| Repo B (Service B) |
+------------------+
        ^
        |
+------------------+
| Repo C (Service C) |
+------------------+
```

**Advantages:**
- Independent versioning and releases for each repository.
- Clear separation of concerns and boundaries between services.

**Disadvantages:**
- Increased overhead in managing multiple repositories.
- More complex dependency management and coordination between repositories.

#### **Mono-Repo Strategy**

**Concept:**
In a mono-repo strategy, all projects or services are stored in a single repository. This approach allows teams to manage and version control all parts of the system together.

**Structure:**

```
+-------------------------+
|          Mono-Repo      |
| +---------------------+ |
| | Service A           | |
| +---------------------+ |
| | Service B           | |
| +---------------------+ |
| | Service C           | |
| +---------------------+ |
+-------------------------+
```

**Advantages:**
- Simplified dependency management with everything in one place.
- Easier to perform atomic changes across multiple services.

**Disadvantages:**
- Can become large and complex as the number of projects grows.
- Requires robust tooling to handle large repositories and avoid performance issues.

### **Cross Reference: Multi-Repo vs. Mono-Repo with Git Flow vs. Trunk-Based Development**

**Multi-Repo with Git Flow:**

```
+------------------+      +------------------+
| Repo A (Git Flow)|      | Repo B (Git Flow)|
+------------------+      +------------------+
        |                        |
        v                        v
+------------------+      +------------------+
| master           |      | master           |
| develop          |      | develop          |
| Feature Branches |      | Feature Branches |
+------------------+      +------------------+
```

**Multi-Repo with Trunk-Based Development:**

```
+------------------+      +------------------+
| Repo A (Trunk)   |      | Repo B (Trunk)   |
+------------------+      +------------------+
        |                        |
        v                        v
+------------------+      +------------------+
| trunk            |      | trunk            |
| Short-Lived B.   |      | Short-Lived B.   |
+------------------+      +------------------+
```

**Mono-Repo with Git Flow:**

```
+----------------------+
| Mono-Repo (Git Flow) |
+----------------------+
        |
        v
+------------------+
| master           |
| develop          |
| Feature Branches |
+------------------+
```

**Mono-Repo with Trunk-Based Development:**

```
+----------------------+
| Mono-Repo (Trunk)    |
+----------------------+
        |
        v
+------------------+
| trunk            |
| Short-Lived B.   |
+------------------+
```

final notes:
- **Git Flow** fits well with **multi-repo** for clear branch management but can be complex in a mono-repo setup.
- **Trunk-Based Development** is simpler and often preferred with **mono-repo** due to its continuous integration model, but it can also work with **multi-repo** if managed carefully.
  
### **Git**
This is a core concept for Devops engineers and there will be a whole note page dedicated to this please see [[GIT notes]]
### **CI/CD**
This is a core concept for Devops engineers and there will be a whole note page dedicated to this please see [[CI-CD & Workflow Orchestration NOTES]]


  
---
# section 2 ) DataOps

## **What is DataOps actually?**







---
# section 3 ) ML-OPS

## **What is ML-OPS actually?**