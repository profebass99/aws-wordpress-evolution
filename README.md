# AWS-Wordpress-Evolution
We are going to be evolving the popularly known web application wordpress into an elastic and scalable architecture.
The architecture will start with a manually built single instance(MONOLITH), running the application and database over the stages of the demo you will evolve this until its a scalable and resilient architecture of 6 stages, each implementing additional components of the architecture

* Phase 1 - Setting up the environment and manually build wordpress
* Phase 2 - Use automation by configuring a Launch Template
* Phase 3 - Migrate the DB into RDS and Update the LT
* Phase 4 - Create a WP filesystem into EFS and Update the LT(to store media contents of your wordpress) 
* Phase 5 - Enable elasticity via a ASG & ALB and fix wordpress (hardcoded WPHOME IP)
* Phase 6 - tidy up your environs



## Steps & Instructions

- [Phase1](https://github.com/profebass99/aws-wordpress-evolution/blob/main/Design%20Steps%20%26%20instructions/Phase-1.md)
- [Phase2](https://github.com/acantril/learn-cantrill-io-labs/blob/master/aws-elastic-wordpress-evolution/02_LABINSTRUCTIONS/STAGE2%20-%20Automate%20the%20build%20using%20a%20Launch%20Template.md)
- [Phase3](https://github.com/acantril/learn-cantrill-io-labs/blob/master/aws-elastic-wordpress-evolution/02_LABINSTRUCTIONS/STAGE3%20-%20Add%20RDS%20and%20Update%20the%20LT.md)
- [Phase4](https://github.com/acantril/learn-cantrill-io-labs/blob/master/aws-elastic-wordpress-evolution/02_LABINSTRUCTIONS/STAGE4%20-%20Add%20EFS%20and%20Update%20the%20LT.md)
- [Phase5](https://github.com/acantril/learn-cantrill-io-labs/blob/master/aws-elastic-wordpress-evolution/02_LABINSTRUCTIONS/STAGE5%20-%20Add%20an%20ELB%20and%20ASG.md)
- [Phase6](https://github.com/acantril/learn-cantrill-io-labs/blob/master/aws-elastic-wordpress-evolution/02_LABINSTRUCTIONS/STAGE6%20-%20CLEANUP.md)
