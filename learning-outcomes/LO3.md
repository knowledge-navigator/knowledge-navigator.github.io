# Learning Outcome 3 - Personal Leadership
> You acquire skills required for your future career. You are aware of multiple career paths and can reflect which ones fit best, considering your (potential) skills and ambitions. You are aware of developments in software engineering and can signal trends. 

*There are plenty of available career paths within ICT. You find roles and careers that fit with your ambitions and demonstrate that you can reflect upon your current skills to determine the development that is needed to pursue the career you foresee. You do this by setting personal and technical development goals and plan which activities are needed to achieve them. The activity of preparing for graduation or finding a minor should be part of your plan (for instance, by finding minors or graduation assignments that fit your ambitions).*

# Available Career Paths
> Potential career options for ICT.

List of career options related directly to Software Engineering:
- Full-Stack Developer
- Back-End Developer
- Front-End Developer
- Mobile Development
- DevOps Specialist
- Embedded Developer
- Data Engineer
- Machine Learning Specialist

While there are many more specialist options such as *cloud infrastructure engineer* or *database administrator*, I consider these to be too specific and would rather focus on a broad career option for now, such as a full-stack developer.

# Best Career Paths
> Career paths that fit best for me considering my ambitions and skills.

I am currently working part-time as an *Embedded Developer* at *Lely*, as part of the *Vector CD* team, contributing to their flagship **cow feeding robot**. For this I have already racked up roughly a year of experience in *Embedded Development*. This experience was useful in helping narrow down which career path is for me, since I learned that embedded systems is not an area I would want to dedicate my life to.

At this moment, I am very completionist-oriented person, thus want to be involved in all aspects of development of a product. This makes me perfectly suited for a full-stack developer position, although I would also be interested in the involvment of other components such as mobile development.

# Personal & technical Development Goals/Plans
> Development required to persue the career I forsee, through setting personal and technical goals.

I have specifically chosen modern programming languages (`Rust`) and frameworks (`Nuxt`) that are new to me this semester for my projects, in order to broaden my knowledge and better set me up for a career with many options. Although it is important that I also specialize in some technologies, I feel like I can achieve this with all the tools that I selecting with enough time dedicated to them.

## Finding Intership
~~TODO~~

# Development in Software Engineering & Trends
> My awareness in developments of Software Engineering and signalling trends.

The research that I did on rendering patterns perfectly demonstrated my interests and knowledge in developments of software engineering. The research was specifically for frontend UIs and led to the decision to use *Static-Site Generation (SSG) with Hydration* for the frontend service of my personal project rather than *SPA* with common frameworks such as `React`. 

See my research for further details on this:
https://knowledge-navigator.github.io/additional-docs/RP.html

Additionally, my decision to use `Rust` and `Warp` was primarily based on the significant advantages that these technologies provided. My research and design choices for the backend web server can be found here ([1](../additional-docs/WS-TS.md) & [2](../additional-docs/IMPL-API.md)). They demonstrate how I have looked into common practices in the industry and applied these to my API. Examples include:
- clean HTTP request processing
- using a `store` struct to create and remove data on demand
- returning custom errors (as opposed to just throwing inferred exceptions)
- using `Arc<T>` to support multi-threading (sharing data among threads)
- using `RwLock<T>` to support locking write actions but always allowing read actions of data