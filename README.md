# InterCharge (Backend)

This application provides a REST API for the _intercharge_ app and the _Ladestationsfinder_. It also provides a
 cron job scheduled data importer for _eRoamingEVSEData_ and _eRoamingEVSEStatus_ services. 
 
## Server Configuration

### Certificates

Before you can start any server, you have to provide a certificate and a private key for the communication with
 the hubject system under `certificates`.
 
**Notation**

````
  root
    - certificates/
        - private.key
        - private.crt

````

### Environment variables

The application needs some environment variables for configuration.

````
  ENVIRONMENT={development|production}
  HBS_EVSE_DATA_ENDPOINT={string}
  NODE_TLS_REJECT_UNAUTHORIZED=0
  HBS_EVSE_STATUS_ENDPOINT={string}
  DB_NAME={string}
  DB_DIALECT=mysql
  DB_USERNAME={string}
  DB_PWD={string}
  DB_HOST={string}

````
 
 **NODE_TLS_REJECT_UNAUTHORIZED** has to be set to 0. Otherwise the self-signed certificates for the hubject 
 communication will not work.
 
## Deployment (AWS)

### VPN (VPC)
http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/vpc-rds.html#vpc-rds-create-env TODO

### .ebextensions
`deployment.config` TODO; description

### RDS access
The security group of the EC2 instance has to be added to the security group of RDS (http://serverfault.com/a/655124). Otherwise RDS will not be accessible from the EC2 instance.

````
Type         | Protocol | Port Range | Source
-------------+----------+------------+-----------
MYSQL/Aurora | TCP      | 3306       | sg-?????

````

## Running node.js server

To start the server use `ENVIRONMENT={development|production} ... DB_HOST={string} npm start` in command line.

## Stack
The application is implemented in TypeScript. For the server implementation express.js is used. The framework sequelize 
provides the orm implementation. 

### TypeScript

The documentation for TypeScript can befound [here](https://www.typescriptlang.org/docs/tutorial.html)

### express.js (server framework)

Documentation for the integrated ORM _Sequelize_ can be found [here](http://expressjs.com/en/4x/api.html)

### sequelize (orm)

Documentation for the integrated ORM _Sequelize_ can be found [here](http://docs.sequelizejs.com/en/latest/)

### ORM wrapper for sequelize

For simplicity and to prevent an interface chaos for the interaction of _TypeScript_ and _Sequelize_, a wrapper on top 
of both is implemented. The implementation is currently found in `orm/`.

#### Definition of database models

To define a database model you have to annotate the class(which represents you specific entity) with the `Table` and
`Column` annotations. `Table` for defining the entity and `Column` for defining the column/property of the entity.
 For example:

````

@Table
class Person {

  @Column
  @PrimaryKey
  id: number;

  @Column
  name: string;

}


````

#### Associations

For Relations between entities there some annotations like `BelongsTo`, `HasOne` or `BelongsToMany` that can be used. To define
foreign keys, use the `ForeignKey` annotation. 

**Many-To-Many**

````

@Table
class Person {

  ... 
  
  @BelongsToMany(() => Person, () => Friend)
  groups: Group[];
  
}

@Table
class Group {

  ...

  @BelongsToMany(() => Person, () => Friend)
  persons: Person[];
  
}

@Table
class PersonGroup {

  @ForeignKey(() => Person)
  personId: number;
  
  @ForeignKey(() => Group)
  groupId: number;

}


````


<!-- TODO -->