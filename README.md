# aws-wordpress-evolution
We are going to be evolving the popularly known web application wordpress into an elastic and scalable architecture
The architecture will start with a manually built single instance, running the application and database over the stages of the demo you will evolve this until its a scalable and resilient architecture of 6 stages, each implementing additional components of the architecture

* Phase 1 - Setting up the environment and manually build wordpress
* Phase 2 - Use automation by configuring a Launch Template
* Phase 3 - Migrate the DB into RDS and Update the LT
* Phase 4 - Create a WP filesystem into EFS and Update the LT(to store media contents of your wordpress) 
* Phase 5 - Enable elasticity via a ASG & ALB and fix wordpress (hardcoded WPHOME IP)
* Phase 6 - tidy up your environs
