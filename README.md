# Lab 2

## Demo Video

[Youtube Link](https://youtu.be/sF1HTsj0zmQ)

## Reflection Questions

1. **What changes did you make to the order-service and product-service to comply with the Configurations and Backing Services factors of the 12-Factor App methodology?**

    Configurations:

    I had to create an untracked .env file for both order-service and product-service to externalize the hard-coded configuration from the code (ie. RabbitMQ connection strings, ports, etc.) into environment variables. It is an important pillar to store the configuration outside code so that the correct configurations can be applied across multiple environments (development, production, etc.) on the same deployment. I made sure to keep the .env files excluded from version control to follow good security practices, as environment variables can include sensitive information like tokens.

    Backing Services:

    The backing service we have in this application is RabbitMQ. Since third party services in a 12-pillar architecture is an attached resource just like any other microservice, it is important to isolate them in the same manner so that any attachment, detachment, or modification of said backing services in the distributed system will not affect the code or any other service they interact with. To fulfill this pillar, RabbitMQ is hosted on its own separate VM just like the other 3 services, with an inbound port rule configured to allow order service to setup a connection string in its environment variables to interact with its event bus through the publish/subscribe model.

    `RABBITMQ_CONNECTION_STRING=amqp://your_username:your_password@<VM_IP>:5672/`

    Therefore, once an order is made, the created order event is published to the RabbitMQ event bus queue so that any other service subscribed to its topic can be alerted and act on it accordingly without communicating directly between each other. It is all facilitated by RabbitMQ, so that if any communication failure happens, the issue can be isolated to RabbitMQ instead of trying to diagnose complex communication configurations between each service in the distributed system.

2. Why is it important to use environment variables instead of hard-coding configurations in your application?

    Distinct separation of configurations from the code is important in the 12 pillars design system because configuration can vary across environments, while code should not. Externalized config allows you to be able to deploy the same app to different platforms or cloud providers (ie. Azure, Heroku, Render, etc.) by simply changing the environment variables, thus also supporting CI/CD pipelines. Furthermore, since no configs are in the code, it is much easier to switch environments or update configs as it will not require the entire app to be redeployed to apply those changes. Finally, configs often contain extremely sensitive data like tokens that should never be visible in the code, instead kept isolated from version control through untracked and ignored .env files.

3. **Why is it important to have separate repositories for each microservice? How does this help maintain independence and scalability of each service?**

    Separate repositories per service aligns with the isolated codebase principle in the 12 pillars that prevents crossed dependencies between services. In addition, each microservice can be deployed independently to have their own version control history, CI/CD pipeline, and updates without affecting any other app in the system. In addition, with automated cloud solutions, services can horizontally scale according their own specific load. High traffic services can scale up their instances while low traffic ones can stay minimal rather than having to vertically scale the entire app. Furthermore, in the modern day it is common for a distributed system to use different frameworks and different languages; one service could be built on Flask, and another in Node.js. By separating codebases, the developer can focus on using the best tools rather than compromising for ease of implementation within one code base. Ultimately, it all supports DevSecOps and DevOps practices that allow teams to be responsible for building and deploying their own independent services without depending on other services enabling them. When teams are responsible only for their own isolated codebase, they can also scale their team and hire for their specific needs.

## Links

[Order Service repo](https://github.com/AliceYangAC/order-service)
[Product Service repo](https://github.com/AliceYangAC/product-service)
[Store Front repo](https://github.com/AliceYangAC/store-front)

## Notes

- I initially followed the instructions in order in the Lab 2 README before realizing that doing so in that order would only work for local setup. In the future, it may be helpful to distinguish the order of setup for VMs vs local; ie. if you are trying to host the 4 services on VMs, only replace the new codebases for the 3 services (excl RabbitMQ), create their .env and exclude it from tracking in .gitignore, and publish their repos before doing any of the other steps (setup the .env, installing the dependencies within each service, etc.). Of course, you did demo this during class so you absolutely did prepare us, but it took a little bit of fiddling around before I remembered the right order of things. :-)
- It could be beneficial to add a section to the lab instructions about if you are unable to create 4 VMs due to the limitations of regions and Azure student accounts.
- In the section `2. Use dependency isolation.` under Factor 2 in the instructions, it could be helpful to clarify to students that they need to rerun the npm setup that happened in the root directory in Lab 1 in each of the remote VMs that require it (the ones based on Javascript), since as separate servers they each require the config.

    ```text
    sudo apt update
    sudo apt install npm
    ```
