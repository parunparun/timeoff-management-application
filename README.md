# Solution to the Challenge

The application GitHub repo was forked to my GitHub repo (https://github.com/parunparun/timeoff-management-application). 

A feature branch (nodejs-fix-elasticbeanstalk) was created for fixing issues found during deployment. File changed and added were - 

.npmrc - unsafe-perm=true was added to make it work with Nodejs version 14. The application still fails with Nodejs version 16. Further investigation is needed.
.ebextensions/alb-http-to-https-redirection-full.config - This adds 443 listener to the Load Balancer as well as HTTP to HTTPS redirection.

The application was deployed to an Elastic BeanStalk HA environment running Nodejs version 14 thus creating a Load Balancer as well as an Auto-Scaling group.

CodePipeline was used to fully automate the deployment to the Elastic BeanStalk evironment. 

After deployment I was able to be navigate to the home page of the application.

** Issues faced during deployment - **

Prior to deploying it to the Elastic BeanStalk environment I tried deploying it to an EC2 instance and verified whether the application starts.

Following command needed to be executed in the EC2 instance 

```bash
sudo yum install -y gcc-c++ make git
git clone https://github.com/parunparun/timeoff-management-application.git
curl -sL https://rpm.nodesource.com/setup_14.x | sudo -E bash -
sudo yum install -y nodejs
cd timeoff-management-application
.npmrc file modified
npm install - 
npm start
```
The above action resulted in the application listening at port 3000. curl http://localhost:3000 returned success.

Using AWS Certificate Manager a public certificate was created so that while creating a Load Balancer listener on port 443 the certificate can be provided.

To make BeanStalk listen to HTTPS request and redirect HTTP to HTTPS I had to add the file alb-http-to-https-redirection-full.config to the .ebextensions folder.

** Improvement ** -

Both code pipeline and BeanStalk were created manually.

For creating the code pipeline a BeanStalk environment already needs to be stood up.

Creation of both the code pipeline and the BeanStalk could have been automated using CloudFormation or Terraform.


# TimeOff.Management

Web application for managing employee absences.

<a href="https://travis-ci.org/timeoff-management/timeoff-management-application"><img align="right" src="https://travis-ci.org/timeoff-management/timeoff-management-application.svg?branch=master" alt="Build status" /></a>

## Features

**Multiple views of staff absences**

Calendar view, Team view, or Just plain list.

**Tune application to fit into your company policy**

Add custom absence types: Sickness, Maternity, Working from home, Birthday etc. Define if each uses vacation allowance.

Optionally limit the amount of days employees can take for each Leave type. E.g. no more than 10 Sick days per year.

Setup public holidays as well as company specific days off.

Group employees by departments: bring your organisational structure, set the supervisor for every department.

Customisable working schedule for company and individuals.

**Third Party Calendar Integration**

Broadcast employee whereabouts into external calendar providers: MS Outlook, Google Calendar, and iCal.

Create calendar feeds for individuals, departments or entire company.

**Three Steps Workflow**

Employee requests time off or revokes existing one.

Supervisor gets email notification and decides about upcoming employee absence.

Absence is accounted. Peers are informed via team view or calendar feeds.

**Access control**

There are following types of users: employees, supervisors, and administrators.

Optional LDAP authentication: configure application to use your LDAP server for user authentication.

**Ability to extract leave data into CSV**

Ability to back up entire company leave data into CSV file. So it could be used in any spreadsheet applications.

**Works on mobile phones**

The most used customer paths are mobile friendly:

* employee is able to request new leave from mobile device

* supervisor is able to record decision from the mobile as well.

**Lots of other little things that would make life easier**

Manually adjust employee allowances
e.g. employee has extra day in lieu.

Upon creation employee receives pro-rated vacation allowance, depending on start date.

Email notification to all involved parties.

Optionally allow employees to see the time off information of entire company regardless of department structure.

## Screenshots

![TimeOff.Management Screenshot](https://raw.githubusercontent.com/timeoff-management/application/master/public/img/readme_screenshot.png)

## Installation

### Cloud hosting

Visit http://timeoff.management/

Create company account and use cloud based version.

### Self hosting

Install TimeOff.Management application within your infrastructure:

(make sure you have Node.js (>=4.0.0) and SQLite installed)

```bash
git clone https://github.com/timeoff-management/application.git timeoff-management
cd timeoff-management
npm install
npm start
```
Open http://localhost:3000/ in your browser.

## Run tests

We have quite a wide test coverage, to make sure that the main user paths work as expected.

Please run them frequently while developing the project.

Make sure you have Chrome driver installed in your path and Chrome browser for your platform.

If you want to see the browser execute the interactions prefix with `SHOW_CHROME=1`

```bash
USE_CHROME=1 npm test
```

(make sure that application with default settings is up and running)

Any bug fixes or enhancements should have good test coverage to get them into "master" branch.

## Updating existing instance with new code

In case one needs to patch existing instance of TimeOff.Managenent application with new version:

```bash
git fetch
git pull origin master
npm install
npm run-script db-update
npm start
```

## How to?

There are some customizations available.

## How to amend or extend colours available for colour picker?
Follow instructions on [this page](docs/extend_colors_for_leave_type.md).

## Customization

There are few options to configure an installation.

### Make sorting sensitive to particular locale

Given the software could be installed for company with employees with non-English names there might be a need to
respect the alphabet while sorting customer entered content.

For that purpose the application config file has `locale_code_for_sorting` entry.
By default the value is `en` (English). One can override it with other locales such as `cs`, `fr`, `de` etc.

### Force employees to pick type each time new leave is booked

Some organizations require employees to explicitly pick the type of leave when booking time off. So employee makes a choice rather than relying on default settings.
That reduce number of "mistaken" leaves, which are cancelled after.

In order to force employee to explicitly pick the leave type of the booked time off, change `is_force_to_explicitly_select_type_when_requesting_new_leave`
flag to be `true` in the `config/app.json` file.

## Use Redis as a sessions storage

Follow instructions on [this page](docs/SessionStoreInRedis.md).

## Feedback

Please report any issues or feedback to <a href="https://twitter.com/FreeTimeOffApp">twitter</a> or Email: pavlo at timeoff.management

