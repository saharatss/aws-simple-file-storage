# Simple File Storage Webservice

This project is a demonstration of a simple, secure, reliable, and scalable 3-tier web application development integrated with AWS services.

## Features
- User authentication/management: registration, login/logout, signed in via social account, e.g., Google account
- File storing: uploading/downloading files, file ownership applied, file uploading optimized, file life-cycle applied
- File serving speed up via Content Delivery Network (CDN/AWS CloudFront)
- Automatically create file thumbnails for image files
- Secured APIs support communication between frontend, backend, and other applications
- Dynamically scales server resources depending on usage demand
- HTTPS, SSL/TLS applied
- Inbound/Outbound firewall applied
- Hidden server public IP addresses
- Hashed password before storing it in the database

## Technologies Used

Development Frameworks

- [Django (Python web framework)](https://github.com/django/django)
- [React (Frontend web framework)](https://react.dev)

Database

- MySQL

Amazon Web Services

- Amazon Route 53
- Amazon Elastic Load Balancing (ELB)
- Amazon Auto Scaling
- Amazon EC2
- Amazon Simple Storage Service (S3)
- Amazon CloudFront
- Amazon Lambda
- Amazon Relational Database Service (RDS)
- Amazon Virtual Private Cloud (VPC)
- Amazon Simple Notification Service (SNS)
- Amazon Certificate Manager (ACM)

## System Architecture

System Architecture Diagram

![System Architecture Diagram](/docs/images/architecture-diagram.png)

Amazon Web Services:

**1. AWS Route 53** - Domain name registration for saharatss.org, records:

- `drive.saharatss.org` → routes traffic to the load balancer.
- `drive-media.saharat.org` → routes traffic to CloudFront for serving static or user-uploaded files.

**2. Load Balancer**

Supports multiple Availability Zones (multi-AZ), applies SSL/TLS certificates to the traffic, and routes traffic to specific server instances through an auto-scaling group with weighted traffic used.

- `*` (default) → forward to Frontend server instances for the primary user interaction interface.
- `/admin/*` → forward to Backend server instances for administrative purposes.
- `/api/*` → forward to Backend server instances for intercommunication between frontend, backend, and other applications.

**3. Auto Scaling Group** - Optimizing server resource scaling depends on usage demand.

It supports multiple Availability Zones (multi-AZ), dynamically scales server resources up or down, applies to both frontend and backend and performs instance health checks.

All server instances (AWS EC2 instances) are spun up from the prepared startup bash script for installing all dependencies, downloading source code, and setting up the environment including creating systemctl services and defining credentials and secret keys.

Notify developers when the resources are scaling via email.

**4. AWS EC2** - Running server instances

Backend: Processes business logic:

- **Authentication System:** register, login, logout, social login, file ownership handler, and session management
- **File handler:** upload/download/edit/delete
- **APIs handler:** create/list/view/edit/upload files and users
- **Administrative supports:** view/edit/delete files and users via web user interface

Frontend: Serving users web user-interface pages:

- Home page
- User login page
- User logout page
- User registration page
- User information page (username, first name, last name, and password changing)

**5. AWS S3** - Storing all user/server files

Main file directory structure:

- `server_files/` → for storing server files such as frontend and backend server files.
- `statics/` → for storing static web page files such as CSS, JavaScript, and images.
- `user_upload/` → for storing user-uploaded files.
- `user_upload_t/` → for storing user-uploaded image thumbnails. These files are automatically created by the Lambda function.

File Life-cycle:

- Day 0: Objects uploaded
- Day 75: Objects move to Standard-IA
- Day 365: Objects move to Glacier Deep Archive
- Day 730: Objects expire (Delete the objects)

**6. AWS CloudFront** - Content Delivery Network (CDN), serving frontend static files and user-uploaded files, and reducing the workload on the backend server."

Connected directly to the domain name `drive-media.saharat.org`

**7. AWS Lambda** - Automatically create a thumbnail image for the uploaded image file

Automatic triggering when files were uploaded to `user_upload/` folder in the S3 bucket, then resize the image and save it to `user_upload_t/` folder in the S3 bucket.

**8. AWS Relational Database Service (RDS)** - MySQL server instance with multi-az supports

**9. AWS SNS** - Notifying the developer when the auto-scaling groups scale up/down the EC2 instances via email

## Website paths and APIs

| User Interface Path | API Path | Authentication | Detail |
| - | - | - | - |
| /account/login | `POST` /api/user/login/ | - | User logging in |
| /account/logout | `POST` /api/user/logout/ | required |  User logging out |
| /account/register | `POST` /api/user/register/ | - |  User registration |
| /account/account/me | `POST` /api/me/edit/ | required |  User basic information changing |
| /account/account/me | `POST` /api/me/password/ | required |  User password changing |
| / | | required | User main page |
| / | `POST` /api/file/upload/start/ | required | Request a pre-signed upload signature for a direct file uploading |
| / | `POST` /api/file/upload/finish/ | required | Update upload status for the uploaded file |
| / | `POST` /api/file/upload/replace/ | required | Replace the specific file |
| / | `POST` /api/file/getlist/ | required | List all files that owned by the particular user |
| / | `POST` /api/file/get/ | required | Get a file basic detail |
| / | `PUT` /api/file/edit/ | required | Edit basic details of the specific file |
| / | `DELETE` /api/file/delete/ | required | Delete the file |
| /admin/ | | required | Administration/Management page |

## Database Schema

Schema for important tables:

![Screenshot of Flow uploading files](/docs/images/database-schema.png)

## Workflow

Files uploading:

![Screenshot of Flow uploading files](/docs/images/flow-file-upload.png)

## Screenshots

User Login page:

![Screenshot of User Login page](/docs/images/ui-user-login.png)

Home page, a user uploaded a file:

![Screenshot of Homepage](/docs/images/ui-file-uploaded.png)

Admin file management page:

![Screenshot of Admin file management](/docs/images/admin-file-list.png)


## Important files

Backend
- Build script `Backend/setup/build.sh`
- Setup script `Backend/setup/setup_prd.sh`
- MySQL connection configuration `Backend/credentials/mysql.cnf`
```
[client]
host     = 
database = 
user     = 
password = 
default-character-set = utf8
```

Frontend
- Build script `Frontend/setup/build.sh`
- Setup script `Frontend/setup/setup_prd.sh`

Lambda function
- `Lambda/package/lambda_function.py`

#

This project is a part of the class CMPE 281 at San Jose State University (SJSU). Developed by Saharat Saengsawang
