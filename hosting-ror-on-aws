## Deploying a Ruby on Rails application to Amazon Web Services

1. Create a rails application    

```bash
 $ rails new widgetly -T -d postgresql
```

2. Inside the application folder, run the following:

```bash
$ bundle package	
```
This will download all of the necessary packages into your application.

3. Create your database as usual  

```bash
$ rails db:create
```

4. Create a simple model `widget`

```bash
$ rails generate model widget name:string count:integer
```

5. Migrate your model

```bash
$ rails db:migrate
```

6. Verify that you can create a `widget` model in your `rails console`

```bash
$ rails c
> Widget.connection
> Widget.create(name: "Foo", count: 4)
   (0.2ms)  BEGIN
  SQL (6.7ms)  INSERT INTO "widgets" ("name", "count", "created_at", "updated_at") VALUES ($1, $2, $3, $4) RETURNING "id"  [["name", "Foo"], ["count", 4], ["created_at", 2017-01-11 19:31:49 UTC], ["updated_at", 2017-01-11 19:31:49 UTC]]
   (4.6ms)  COMMIT
 => #<Widget id: 1, name: "Foo", count: 4, created_at: "2017-01-11 19:31:49", updated_at: "2017-01-11 19:31:49"> 
 >
```


7.  Create a controller for your `widget`

```bash
$ rails generate controller widget index
```

8. Remove the automagically generated code your controller made in your `config/routes.rb` file

```ruby
#Remove this:
get 'widget/index'
#Replace with this:
root "widget#index"
```


9.  Create some sample data in your database in `db/seeds.rb`

```ruby
Widget.create(name: "Foo", count: 4)
Widget.create(name: "Bar", count:3)
Widget.create(name: "Baz", count: 7)

puts "Total Widget count: #{Widget.count}"
```

10. Run your seed file.
  
```bash  
$ rails db:seed
```

11. In your `widget` controller, create an instance variable `@widgets` and populate it with all models in your `widget` table:

```ruby
class WidgetController < ApplicationController
  
  def index
    @widgets = Widget.all
  end

end
```

12. In your `widget` view file `index.html.erb`, write code to iterate over your `widget` models passed from the controller.

```ruby
 <h1>Widget#index</h1>
 <p>Find me in app/views/widget/index.html.erb</p>

 <% @widgets.each do |w| %>
  <h4><em><%= w.name %></em></h4>
  <p>count: <%= w.count %></p>
 <% end %>
```

13. Start up your server and verify sanity:

```bash
$ rails s
```

14. You should see something similar to this:
<img src="http://i.imgur.com/mzokK8c.png" style="border:solid black;">

15.  Good job. Integrate git into your project.

```bash
$ git init
$ git add .
$ git commit -m "Initial commit"
```


## It is now time to push to Amazon Web Services. 

1. Install the Elastic Beanstalk Command-line interface. This is Amazon's service that allows us to create environments and push the app to deployment.

```bash
$ brew install awsebcli
```

2.  Check the `eb` version to verify installation:

```bash
 $ eb --version
 # EB CLI 3.9.0 (Python 2.7.1)
```

4. Inside your application folder at root level, run the following command. This initiates an Elastic Beanstalk instance within your applicaition.

```bash
$ eb init
```

5. Select default values for region

```bash
Select a default region
1) us-east-1 : US East (N. Virginia)
2) us-west-1 : US West (N. California)
3) us-west-2 : US West (Oregon)
4) eu-west-1 : EU (Ireland)
5) eu-central-1 : EU (Frankfurt)
6) ap-south-1 : Asia Pacific (Mumbai)
7) ap-southeast-1 : Asia Pacific (Singapore)
8) ap-southeast-2 : Asia Pacific (Sydney)
9) ap-northeast-1 : Asia Pacific (Tokyo)
10) ap-northeast-2 : Asia Pacific (Seoul)
11) sa-east-1 : South America (Sao Paulo)
12) cn-north-1 : China (Beijing)
13) us-east-2 : US East (Ohio)
14) ca-central-1 : Canada (Central)
15) eu-west-2 : EU (London)
(default is 3): 
```

6.  Create an application name to match your Ruby on Rails applicaiton name.

```bash
Enter Application Name
(default is "widgetly"): widgetly
```

7. Verify that this is a Ruby project

```bash
It appears you are using Ruby. Is this correct?
(y/n): y
```

8. Verify your Ruby version

```bash
Select a platform version.
1) Ruby 2.3 (Puma)
2) Ruby 2.2 (Puma)
3) Ruby 2.1 (Puma)
4) Ruby 2.0 (Puma)
5) Ruby 2.3 (Passenger Standalone)
6) Ruby 2.2 (Passenger Standalone)
7) Ruby 2.1 (Passenger Standalone)
8) Ruby 2.0 (Passenger Standalone)
9) Ruby 1.9.3
(default is 1): 1
```

9.  At this point EB will ask you if you want to integrate their AWS CodeCommit software. At this point decline.

```bash
Note:
 Elastic Beanstalk now supports AWS CodeCommit; a fully-managed source control service. To learn more, see Docs: https://aws.amazon.com/codecommit/
Do you wish to continue with CodeCommit? (y/n) (default is n): n
```

10.  Finally, the process will ask you if you are want to set up SSH for your instance. Also decline this.

```bash
Do you want to set up SSH for your instances?
(y/n): n
```

At this point your project is now associated with the Elastic Beanstalk. You will have to include some folders and files to instruct your Amazon EC2 instance on how you want your environment set up.  Now is a good moment to briefly demonstrate self-congratulations. ![](http://cultofthepartyparrot.com/parrots/parrot.gif)

##Prepping your application for launch

1.  In your root, create a folder named `.ebextensions`.  This folder will contain `config` files that will execute every time your application is deployed and initialized on your EC2 instance.

2.  Within your `.ebextensions` folder, create a file named `packages.config`.  

```yaml
# .ebextensions/packages.config
packages:
  yum:
    postgresql93-devel: []
```

> This is read by Elastic Beanstalk as yaml file. Yaml  files MUST have proper indentation in order to compile and run properly.  The above text must have spaces (NOT TABS) and look exactly as it does above.

3.  Create a `seed.config` file as well.  This will execute the `rake db:seed` function we would normally call before starting our local server.

```yaml
# .ebextensions/seed.config
container_commands:
  01seed:
    command: rake db:seed
```

4.  Next, in your `database.yml` file in the `config/` folder, replace the following code:

```yaml
# config/database.yml
#old code:
production:
  <<: *default
  database: widgetly_production
  username: widgetly
  password: <%= ENV['WIDGETLY_DATABASE_PASSWORD'] %>

#new code:
production:
  <<: *default
  adapter: postgresql
  encoding: unicode
  database: <%= ENV['RDS_DB_NAME'] %>
  username: <%= ENV['RDS_USERNAME'] %>
  password: <%= ENV['RDS_PASSWORD'] %>
  host: <%= ENV['RDS_HOSTNAME'] %>
  port: <%= ENV['RDS_PORT'] %>

```

Again, this is a yaml file. You need to ensure spacing is exact and precise. NO TABS.

**Commit all of your changes with your local git repository before continuing.**

###You are now ready to create the Elastic Beanstalk environment to host your application.  

1.  In your terminal, create an Elastic Beanstalk environment thusly:

```bash
$ eb create
```

This will ask you to name a new environment.  Consider accept the default value:

```bash
Enter Environment Name
(default is widgetly-dev): 
```
Press Return

```bash
Enter DNS CNAME prefix
(default is widgetly-dev): 
```
Press Return

```bash
Select a load balancer type
1) classic
2) application
(default is 1): 
```

Elastic Beanstalk will now take over from here.  An instance will be created and deployed to Amazon.

You should see somethign similar to this:

```bash
Creating application version archive "app-a3f0-170111_143357".
Uploading: [##################################################] 100% Done...
Environment details for: widgetly-dev
  Application name: widgetly
  Region: us-west-2
  Deployed Version: app-a3f0-170111_143357
  Environment ID: e-acv24edmry
  Platform: 64bit Amazon Linux 2016.09 v2.3.0 running Ruby 2.3 (Puma)
  Tier: WebServer-Standard
  CNAME: widgetly-dev.us-west-2.elasticbeanstalk.com
  Updated: 2017-01-11 22:34:06.647000+00:00
Printing Status:
INFO: createEnvironment is starting.
INFO: Using elasticbeanstalk-us-west-2-465909818209 as Amazon S3 storage bucket for environment data.
INFO: Created security group named: sg-73954b0b
INFO: Created load balancer named: awseb-e-a-AWSEBLoa-SRVD6P29P0GO
INFO: Environment health has transitioned to Pending. Initialization in progress (running for 35 seconds). There are no instances.
INFO: Created security group named: awseb-e-acv24edmry-stack-AWSEBSecurityGroup-1TZ038BG6N867
INFO: Created Auto Scaling launch configuration named: awseb-e-acv24edmry-stack-AWSEBAutoScalingLaunchConfiguration-KCKRNRU6CR0G
INFO: Added instance [i-0acd4405709589976] to your environment.
INFO: Created Auto Scaling group named: awseb-e-acv24edmry-stack-AWSEBAutoScalingGroup-1ITXSYSTIAEGV
INFO: Waiting for EC2 instances to launch. This may take a few minutes.
INFO: Created Auto Scaling group policy named: arn:aws:autoscaling:us-west-2:465909818209:scalingPolicy:b5bb2a56-c4b0-4ac8-9941-637759bf45f6:autoScalingGroupName/awseb-e-acv24edmry-stack-AWSEBAutoScalingGroup-1ITXSYSTIAEGV:policyName/awseb-e-acv24edmry-stack-AWSEBAutoScalingScaleUpPolicy-19J2PVS28TBYE
INFO: Created Auto Scaling group policy named: arn:aws:autoscaling:us-west-2:465909818209:scalingPolicy:8b9ea2e5-7d84-4357-8fb0-b93a7579ab0c:autoScalingGroupName/awseb-e-acv24edmry-stack-AWSEBAutoScalingGroup-1ITXSYSTIAEGV:policyName/awseb-e-acv24edmry-stack-AWSEBAutoScalingScaleDownPolicy-1JQ668QF1CO1X
INFO: Created CloudWatch alarm named: awseb-e-acv24edmry-stack-AWSEBCloudwatchAlarmHigh-1XGMFUUQH4MDH
INFO: Created CloudWatch alarm named: awseb-e-acv24edmry-stack-AWSEBCloudwatchAlarmLow-1WHUAPBSYI9F5
ERROR: [Instance: i-0acd4405709589976] Command failed on instance. Return code: 1 Output: (TRUNCATED)...ly and accepting
	connections on Unix domain socket "/var/run/postgresql/.s.PGSQL.5432"?
/opt/rubies/ruby-2.3.1/bin/bundle:23:in `load'
/opt/rubies/ruby-2.3.1/bin/bundle:23:in `<main>'
Tasks: TOP => db:migrate
(See full trace by running task with --trace). 
Hook /opt/elasticbeanstalk/hooks/appdeploy/pre/12_db_migration.sh failed. For more detail, check /var/log/eb-activity.log using console or EB CLI.
INFO: Command execution completed on all instances. Summary: [Successful: 0, Failed: 1].
ERROR: Create environment operation is complete, but with errors. For more information, see troubleshooting documentation.
WARN: Environment health has transitioned from Pending to Degraded. Command failed on all instances. Initialization completed 40 seconds ago and took 5 minutes.
```

2.  We will now have to create a separate database server for our application to use.

Head to the Elastic Beanstalk Dashboard [here](https://us-west-2.console.aws.amazon.com/elasticbeanstalk/home?region=us-west-2#/applications)

You should see a card representing Widgetly.

![](http://i.imgur.com/XJ2Ksfi.png)

3.  Click on this card to see specific information about the widgetly environment. On the left you should see an option for `Configuration`. Lets click on that.

Scroll down until you see the Data Tier section

![](http://i.imgur.com/ReDGUlT.png)

4.  Click on the `create a new RDS database` link

Select the following options for your database server:

![](http://i.imgur.com/DyUUcVI.png)

5.  Click the Apply button. This will now take you back to Elastic Beanstalk Dashboard for widgetly. Your Amazon environment will now automagically connect to your newly created database server.  Now is an excellent opportunity to enjoy a delicious La Croix  

 ![delicious La Croix](https://emoji.slack-edge.com/T024JRAUL/pamplemousse/73541d6ce5c02c03.png)
 
6.  Once the `eb` service is done doing magic, you should lastly add your secret key base to your environment.  To do so, let us find your secret key for your application:
 
 ```bash
 $	rails secret
 # 0809e39d228404abdd7f7891b2307bb7edb79f7d77086cc27f58c4146cd9d03bdf0d81aed1b55eb6d3fb9feea4c58adbcfc20abae44d0c8511589156f38e0864
 ```
 
7.  Copy this secret key and send it over to your environment's variables using the `eb` service:
 
 ```bash
 $ eb setenv SECRET_KEY_BASE=0809e39d228404abdd7f7891b2307bb7edb79f7d77086cc27f58c4146cd9d03bdf0d81aed1b55eb6d3fb9feea4c58adbcfc20abae44d0c8511589156f38e0864
 ```
