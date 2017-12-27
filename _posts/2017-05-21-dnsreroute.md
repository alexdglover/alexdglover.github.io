---
title: DNSReRoute - A Full SaaS Platform
categories:
- IT/Software Projects
tags:
- Flask
- single page application
- Python
- MongoDB
- API
- DNS
- redirect
- SaaS
header:
  teaser: /assets/img/DNSReRoute_Architecture.png
---

**Dec 28 2017 Update** OpenShift deprecated their original offering, so the API backend is completely dead. All functionality in the single page app (besides the Auth0 integration) is now broken. 
{: .notice--warning}

About a year ago, I had a use case for a simple redirect. I just needed to
redirect developers from an old Jenkins DNS record (that pointed to an instance
that was destroyed) to a new DNS record (pointing to the new, running Jenkins
instance). Now this is a trivial task if you want to run NGINX or Apache, but
becomes more challenging if you want to avoid that overhead. I tried hosting a
simple JavaScript redirect using S3, but the bucket obviously didn't match my
DNS name, so that didn't work. Route53 doesn't support HTTP redirects. I was at
a loss - how could something so trivial require a whole web server to
accomplish?

I started looking into 3rd party services that could do this, mostly out of
curiosity. I stumbled across [301redirect.it](https://www.301redirect.it/) and
[EasyRedir](https://www.easyredir.com/). 301redirect.it is free, but
easyredir.com charges between $10 and _$80 per month_. I'm sure their service
is awesome, but that was a big enough price tag that I started thinking it
might be worthwhile to try to compete in the same market. Especially since DNS
redirection is so easy.

This is probably the most feature complete software project I've ever done.
It's a "full SaaS platform," meaning it has proper authentication (via
 [Auth0](https://auth0.com/)), billing capabilities (via [Stripe](https://stripe.com/))
 , and is totally self-service for customers to consume. It should
be a turnkey cash cow.

But like most of my projects, I did effectively zero promotion or sales work, so of
course it has zero customers (not just zero paying customers - zero customers
 period). Before I sunset the project forever, I wanted to do this blog post
 and put the site up for sale on Flippa.

Before we breakdown the architecture and application, check out the walkthrough
video or check out the site for yourself:

[DNSReRoute](https://www.dnsreroute.xyz/){: .btn .btn--large .btn--info}
{: .text-center}

{% include video id="Y1rBVcrL3_E" provider="youtube" %}

## Architecture
This architecture is not ideal, so before you "Principal Architect" types try to
mouth off in the comments, know that I'm quite aware of its design flaws.

At a high level, DNSReRoute is comprised of four components:
* RESTful API backend built on Flask running on [OpenShift](https://www.openshift.com/)
* "Dumb" redirect servers running Flask, in an autoscaling group, behind an ELB
in AWS
* A MongoDb instance running on [mLab](https://mlab.com/) with a public
endpoint. Both the RESTful API backend and the "dumb" redirect servers read
from this DB.
* A static, single page app (SPA) using jQuery and hosted on GitHub Pages.

In case that doesn't make any sense, here's a sexy diagram to help you
understand:

<a href="{{ "/assets/img/DNSReRoute_Architecture.png" | absolute_url }}"><img class="text-center" src="{{ "/assets/img/DNSReRoute_Architecture.png" | absolute_url }}"
alt="DNSReRoute architecture" /></a>

Let's do an example walkthrough. Let's pretend we are "BlackLanternStudio" and
we sell our product on Etsy, at `https://www.etsy.com/shop/BlackLanternStudio`.
We want to capture more traffic and retain more brand identity, so we want
`www.BlackLanternStudio.com` to redirect to `https://www.etsy.com/shop/BlackLanternStudio`.
To do this, we would browse to [www.dnsreroute.xyz](https://www.dnsreroute.xyz/),
which takes you to the static front-end site. From here we would create an
account and then create a "route" to handle our redirect, including what
incoming DNS names to expect (inbound routes), and where to redirect that
traffic (outbound routes). All of the actions in the UI are making API calls to
the Flask backend, which then writes that data to the database. Finally, we
would log into our DNS system and create a CNAME record, pointing `www.BlackLanternStudio.com`
to `service.dnsreroute.xyz`. Now when our customers browse to
`www.BlackLanternStudio.com`, they are actually being sent to DNSReRoute's
redirect hosts. The redirect hosts are running some fairly simple Flask code,
basically inspecting the `Host` header to get the inbound DNS name, looking up
the "route" associated with that inbound DNS name in the database, and
redirecting the user's browser to the outbound DNS name.

Originally I was going to host everything on OpenShift to keep things simple,
but using custom and un-predictable customer CNAMEs wouldn't work. In addition,
moving the actual redirect work to a separate stack allows the two components
to scale independently and prevents bad code/failed deploys to the API layer
(where more complex logic resides, more likely to break) from affecting the
 redirect layer (less complex, less likely to break).

## Third Party services

### [Auth0](https://auth0.com/)
Setting up authentication with Auth0 was a little tricky at first, but it has
been bulletproof ever since. Auth0 provides a
[clean Flask example](https://auth0.com/docs/quickstart/backend/python/01-authorization)
that is easy to integrate with your Flask implementation. If I'm being honest,
I think I re-used their authentication wrapper line for line. Most of the time
I spent on Auth0 setup didn't involve any code, but rather all of the ancillary
pieces like SSL certificates, setting up the callback URI, registering domains for
Cross Origin Requests (CORS) in Auth0's web UI, etc.

### [Stripe](https://stripe.com/)
Stripe makes handling subscriptions and one-time payments in your web application
fun. They abstract all of the difficult parts of payment processing and record
keeping, you just have to write your app to consume their APIs for creating new
customers and looking up existing subscriptions. I really like Stripe because
their 'sandbox' or development support is significantly easier than PayPal. With
Stripe, you simply change which API client keys you are using, and you can use any
fake credit card number to test your app. With PayPal, you have to set up a sandbox,
you have to set up sandbox payer/payee accounts, and have to use certain credit
card numbers.

TL;DR stripe is awesome for payment processing.

## Database design
When I first started this project, it had a much smaller scope. Based solely
on storing route information, I decided a NoSQL database like MongoDB would be
a perfect fit. Unfortunately, as I added features like user management,
organizations, and subscriptions, I had to build relations between my
tables/collections. So I've effectively created a relational DB on top of a
non-relational DB, which is a little embarrassing to say the least.

DNSReRoute uses four collections (like tables, if you're not familiar):
* orgs - comprised of an ID, orgName, and subscription
* users - comprised of an ID, userName (used for display purposes), userEmail
(used for auth purposes), and orgId as a "foreign key"
* routes - comprised of an ID, incomingRoute, outgoingRoute, type (301 vs 302
redirect), and orgId as a "foreign key"
* subscriptions - comprised of an ID, subscriptionName (matches the subscription
name in Stripe), title (for display purposes), and some boilerplate HTML
describing the subscription, so it can be consumed consistently in the UI

For the handful of people out there who can read an ER diagram and appreciate
them, here it is:

<a href="{{ "/assets/img/DNSReRoute_ER.png" | absolute_url }}"><img class="text-center" src="{{ "/assets/img/DNSReRoute_ER.png" | absolute_url }}"
alt="DNSReRoute ER diagram" /></a>

## Static SPA Front-end
The front-end is made with DashGum, a pre-made Dashboard template built using
bootstrap that I found on [colorlib](https://colorlib.com/wp/free-bootstrap-admin-dashboard-templates/).
All of the logic is written in straight JavaScript and jQuery, nothing fancy
like Angular or React. A true front-end developer would probably be appalled
with my code, but it works and I was able to knock out a prototype very quickly.
 The page itself is actually hosted on GitHub pages in a public repo, so the
all of the code is available for you to peruse if you're interested:

[Static SPA Repo](https://github.com/alexdglover/dnsreroute-site/tree/gh-pages){: .btn .btn--info .btn--large}
{: .text-center}


## API Backend
The API backend is a Flask app running on [OpenShift](https://www.openshift.com/).
I chose OpenShift for this project as OpenShift will allow you to keep a certain
number of free apps running 24/7, so long as you have a credit card on file. This
is in contrast to Heroku (which I also love) that puts your "dynos" to sleep when
they become inactive.

The API backend can be hit at [https://dnsrerouteprod-dnsreroute.rhcloud.com](https://dnsrerouteprod-dnsreroute.rhcloud.com).
You can browse through the [Swagger API doc](https://www.dnsreroute.xyz/swagger/index.html)
to get a feel for the routes. Note that you won't be able to send any API calls
via the Swagger doc unless the SAML Bearer token set. If you open the Swagger
doc from the DNSReRoute dashboard (by clicking on the "API Playground" link in the left sidebar), it will set your SAML Bearer token for you.

As for the code, all of the relevant logic is contained in `flaskapp.py`:

```python
import os
from datetime import datetime
from urlparse import urlparse
from flask import Flask, request, flash, url_for, redirect, \
     render_template, abort, send_from_directory, jsonify, _request_ctx_stack
import jwt
import requests
import base64
from functools import wraps
from werkzeug.local import LocalProxy
from flask.ext.cors import CORS, cross_origin
import pymongo
from pymongo import MongoClient, IndexModel, ASCENDING, DESCENDING, errors
from bson.json_util import dumps
import json
from bson.objectid import ObjectId
import stripe

env = os.environ
client_id = os.environ['AUTH0_CLIENT_ID']
client_secret = os.environ["AUTH0_CLIENT_SECRET"]
stripe.api_key = os.environ["STRIPE_API_KEY"]

requestorEmail = ""

app = Flask(__name__)
app.config.from_pyfile('flaskapp.cfg')
CORS(app)

# Format error response and append status code.
def handle_error(error, status_code):
  resp = jsonify(error)
  resp.status_code = status_code
  return resp

def requires_auth(f):
  @wraps(f)
  def decorated(*args, **kwargs):
    auth = request.headers.get('Authorization', None)
    if not auth:
      return handle_error({'code': 'authorization_header_missing', 'description': 'Authorization header is expected'}, 401)

    parts = auth.split()

    if parts[0].lower() != 'bearer':
      return handle_error({'code': 'invalid_header', 'description': 'Authorization header must start with Bearer'}, 401)
    elif len(parts) == 1:
      return handle_error({'code': 'invalid_header', 'description': 'Token not found'}, 401)
    elif len(parts) > 2:
      return handle_error({'code': 'invalid_header', 'description': 'Authorization header must be Bearer + \s + token'}, 401)

    # This is the bearer token
    token = parts[1]

    try:
        payload = jwt.decode(
            token,
            base64.b64decode(client_secret.replace("_","/").replace("-","+")),
            audience=client_id
        )
    except jwt.ExpiredSignature:
        return handle_error({'code': 'token_expired', 'description': 'token is expired'}, 401)
    except jwt.InvalidAudienceError:
        return handle_error({'code': 'invalid_audience', 'description': 'incorrect audience, expected: ' + client_id}, 401)
    except jwt.DecodeError:
        return handle_error({'code': 'token_invalid_signature', 'description': 'token signature is invalid'}, 401)
    except Exception:
        return handle_error({'code': 'invalid_header', 'description':'Unable to parse authentication token.'}, 400)

    endpoint = "https://alexdglover.auth0.com/tokeninfo"
    headers = {"Authorization":"Bearer " + token}
    data = {"id_token": token}
    global requestorEmail
    try:
        requestorEmail = requests.post(endpoint,data=data,headers=headers).json()['email']
    except Exception as e:
        return handle_error({'code': 'failed_user_lookup', 'description': 'Unable to look up user with that token'})

    _request_ctx_stack.top.current_user = user = payload
    return f(*args, **kwargs)

  return decorated

def connect():
    connection = MongoClient(os.environ['OPENSHIFT_MONGODB_DB_URL'],int(os.environ['OPENSHIFT_MONGODB_DB_PORT']))
    handle = connection["dnsreroute"]
    handle.authenticate(os.environ['OPENSHIFT_MONGODB_DB_USERNAME'],os.environ['OPENSHIFT_MONGODB_DB_PASSWORD'])
    return handle

def initializeDb():
    handle.routes.create_index( [ ("incomingRoute", ASCENDING)], unique=True )
    handle.users.create_index( [ ("userEmail", ASCENDING)], unique=True )

handle = connect()

initializeDb()

# Sets response headers for all requests received. This is needed to allow
# pre-flight OPTIONS requests to get the information they need to do PUTs
# and DELETEs
@app.after_request
def after_request(response):
  response.headers['Access-Control-Allow-Origin'] = request.headers.get('Origin','*')
  response.headers.add('Access-Control-Allow-Headers', 'Content-Type,Authorization')
  response.headers.add('Access-Control-Allow-Methods', 'PUT,POST,OPTIONS,DELETE,GET')
  return response

# Controllers API
@app.route("/")
def home():
    host = request.headers['Host']
    host = host.split(':')[0]
    if host != 'service.dnsreroute.xyz':
        route = handle.routes.find_one({'incomingRoute': host})
        if route:
            outgoingRoute = route['outgoingRoute']
            if route['type'] == "301":
                return redirect(outgoingRoute, 301)
            elif route['type'] == "302":
                return redirect(outgoingRoute)
            else:
                return '{"message": "Error - not able to determine redirect type"}'
        else:
            return '{"message": "Could not find a matching route"}', 404

    else:
      return "The Host header is {hostHeader}.This is the unsecured home page".format(hostHeader=request.headers['Host'])

@app.route("/ping",host="*")
def ping():
    return "All good. You don't need to be authenticated to call this"

@app.route("/secured/ping")
@requires_auth
def securedPing():
    return "All good. You only get this message if you're authenticated"

###################################
###   Route URIs
###################################

@app.route("/routes/byUserEmail/<userEmail>", methods=['GET'])
@requires_auth
def getRoutesByUserEmail(userEmail):
    orgId = handle.users.find_one({"userEmail": userEmail})['orgId']
    print 'orgId is {orgId}'.format(orgId=orgId)
    routes = handle.routes.find({"orgId": ObjectId(orgId)})
    if routes:
        return dumps(routes)
    else:
        message = {"message": "No routes found with that orgId"}
        return jsonify(message), 404

@app.route("/routes/byOrg/<orgId>", methods=['GET'])
@requires_auth
def getRoutesByOrgId(orgId):
    orgId = validateObjectId(orgId)

    if not orgId:
        message = {"message": "Invalid org ID, uanble to convert to ObjectId. Must be a 12-byte input or a 24-character hex string"}
        return jsonify(message), 400

    routes = handle.routes.find({"orgId": orgId})
    if routes:
        return dumps(routes)
    else:
        message = {"message": "No routes found with that orgId"}
        return jsonify(message), 404


@app.route("/routes", methods=['POST'])
@requires_auth
def addRoute():
    if isAuthorized(requestorEmail, 'addRoute'):
        try:
            handle.routes.insert({"orgId": ObjectId(request.values['orgId']), "type": request.values['type'],
                "incomingRoute": request.values['incomingRoute'], "outgoingRoute": request.values['outgoingRoute']})
            return '{"message": "successfully added route"}'
        except pymongo.errors.DuplicateKeyError:
            errorDict = {"message": "Failed to add route - that incoming DNS name is already in use"}
            return jsonify(errorDict), 400
        except Exception as e:
            errorDict = {"message": "Failed to add route. Error message: {error}".format(error=e)}
            return jsonify(errorDict), 400
    else:
        # Not authorized
        errorDict = {"message": "You are not authorized to add another route!"}
        return jsonify(errorDict), 403

@app.route("/routes/<incomingRoute>", methods=['DELETE'])
@requires_auth
def deleteRoute(incomingRoute):
    # Validate incomingRoute is valid first
    route = handle.routes.find_one({"incomingRoute": incomingRoute})
    if route:
        if isAuthorized(requestorEmail, 'deleteRoute', incomingRoute):
            try:
                result = handle.routes.remove({"incomingRoute": incomingRoute})
                return '{"message": "Successfully deleted route"}'
            except Exception as e:
                errorDict = {"message": "Failed to delete route. Error message: {error}".format(error=e)}
                return jsonify(errorDict), 400
        else:
            # Not authorized
            errorDict = {"message": "You are not authorized to delete that route!"}
            return jsonify(errorDict), 403
    # If the route targeted for deletion wasn't found, return a 200 with explanation
    else:
        message = {"message": "Route doesn't exist, but that's OK! HTTP DELETE is an idempotent operation dude"}
        return jsonify(message)

###################################
###   End of Route URIs
###################################

###################################
###   User URIs
###################################

@app.route("/users")
@requires_auth
def getUsers():
    actor = handle.users.find_one({"userEmail": requestorEmail})
    users = handle.users.find({"orgId": ObjectId(actor['orgId'])})
    if users:
        return dumps(users)
    else:
        message = {"message": "No users found"}
        return jsonify(message), 404

@app.route("/users/byOrg/<orgId>")
@requires_auth
def getUsersByOrg(orgId):
    orgId = validateObjectId(orgId)

    if not orgId:
        message = {"message": "Invalid org ID, uanble to convert to ObjectId. Must be a 12-byte input or a 24-character hex string"}
        return jsonify(message), 400

    users = handle.users.find({"orgId": orgId})
    if users:
        return dumps(users)
    else:
        message = {"message": "No users found with that org ID"}
        return jsonify(message), 404

@app.route("/users/<userEmail>", methods=['GET'])
@requires_auth
def getUserByEmail(userEmail):
    user = handle.users.find_one({"userEmail": userEmail})
    if user:
        user['_id'] = str(user['_id'])
        user['orgId'] = str(user['orgId'])
        return dumps(user)
    else:
        message = {"message": "No user found with that email address"}
        return jsonify(message), 404

@app.route("/users/<userEmail>", methods=['PUT'])
@requires_auth
def updateUser(userEmail):
    if requestorEmail == userEmail:
        user = handle.users.find_one({"userEmail": userEmail})
        if user:
            result = handle.users.update_one( { "userEmail": userEmail },
                {
                  "$set": {
                    # "userEmail": userEmail,
                    "userName": request.values['userName'],
                    "orgId": ObjectId(request.values['orgId'])
                  }
                }
            )
            message = {"message": "User updated successfully"}
            return jsonify(message)
        else:
            message = {"message": "No user found with that email address"}
            return jsonify(message), 404
    else:
        message = {"message": "You are not authorized to update that user. You may only update your own user information"}
        return jsonify(message), 403

@app.route("/users", methods=['POST'])
@requires_auth
def addUser():
    if isAuthorized(requestorEmail, 'addUser', request.values['orgId']):
        try:
            handle.users.insert({"userEmail":request.values['userEmail'], "userName":request.values['userName'], "orgId": ObjectId(request.values['orgId']) })
            message = {"message": "Successfully added user"}
            return jsonify(message)
        except pymongo.errors.DuplicateKeyError:
            errorDict = {"message": "User with that email already exists"}
            return jsonify(errorDict), 409
        except Exception as e:
            errorDict = {"message": "Failed to add user. Error message: {error}".format(error=e)}
            return jsonify(errorDict), 400
    else:
        # Not authorized
        errorDict = {"message": "You are not authorized to add another user!"}
        return jsonify(errorDict), 403

@app.route("/users/register", methods=['POST'])
@requires_auth
def registerNewUser():
    try:
        orgId = handle.orgs.insert({"orgName": request.values['userEmail'], "subscription": "freeTier"})
        handle.users.insert({"userEmail":request.values['userEmail'], "userName":request.values['userName'], "orgId": ObjectId(orgId) })
        message = {"message": "Successfully registered user"}
        return jsonify(message)
    except pymongo.errors.DuplicateKeyError:
        errorDict = {"message": "User with that email already exists"}
        return jsonify(errorDict), 409
    except Exception as e:
        errorDict = {"message": "Failed to add user. Error message: {error}".format(error=e)}
        return jsonify(errorDict), 400

@app.route("/users/<userEmail>", methods=['DELETE'])
@requires_auth
def deleteUser(userEmail):
    targetUser = handle.users.find_one({"userEmail": userEmail})
    if targetUser:
        if isAuthorized(requestorEmail, 'deleteUser', userEmail):
            try:
                result = handle.users.remove({"userEmail": userEmail})
                message = {"message": "Successfully deleted user"}
                return jsonify(message)
            except Exception as e:
                errorDict = {"message": "Failed to delete user. Error message: {error}".format(error=e)}
                return jsonify(errorDict), 400
        else:
            # Not authorized
            errorDict = {"message": "You are not authorized to delete that user!"}
            return jsonify(errorDict), 403
    # If the user targeted for deletion wasn't found, return a 200 with explanation
    else:
        message = {"message": "User doesn't exist, but that's OK! HTTP DELETE is an idempotent operation dude"}
        return jsonify(message)

###################################
###   End of User URIs
###################################

###################################
###   Org URIs
###################################
@app.route("/orgs", methods=['POST'])
@requires_auth
def addOrg():
    try:
        orgId = handle.orgs.insert({"orgName": request.values['userEmail'], "subscription": "freeTier"})
        return '{"message": "Successfully added org"}'
    except pymongo.errors.DuplicateKeyError:
        errorDict = {"message": "Org already exists"}
        return jsonify(errorDict), 400
    except Exception as e:
        errorDict = {"message": "Failed to add org. Error message: {error}".format(error=e)}
        return jsonify(errorDict), 400

@app.route("/orgs/<orgId>", methods=['PUT'])
@requires_auth
def updateOrg(orgId):
    orgId = validateObjectId(orgId)

    if not orgId:
        message = {"message": "Invalid org ID, uanble to convert to ObjectId. Must be a 12-byte input or a 24-character hex string"}
        return jsonify(message), 400

    try:
        org = handle.orgs.find_one( {"_id": orgId} )

        if org:
            result = handle.orgs.update_one( {"_id": orgId},
                {
                  "$set": {
                    "orgName": request.values['orgName']
                  }
                }
            )
            message = {"message": "Org updated successfully"}
            return jsonify(message)
        else:
            message = {"message": "No org with that id"}
            return jsonify(message), 404
    except Exception as e:
        errorDict = {"message": "Failed to update org. Error message: {error}".format(error=e)}
        return jsonify(errorDict), 400

@app.route("/orgs/<orgId>", methods=['GET'])
@requires_auth
def getOrg(orgId):
    orgId = validateObjectId(orgId)

    if not orgId:
        message = {"message": "Invalid org ID, uanble to convert to ObjectId. Must be a 12-byte input or a 24-character hex string"}
        return jsonify(message), 400

    org = handle.orgs.find_one({"_id": orgId})
    if org:
        org['_id'] = str(org['_id'])
        return dumps(org)
    else:
        message = {"message": "No org found with that orgId"}
        return jsonify(message), 404

@app.route("/orgs/<orgId>", methods=['DELETE'])
@requires_auth
def deleteOrg(orgId):
    orgId = validateObjectId(orgId)

    if not orgId:
        message = {"message": "Invalid org ID, uanble to convert to ObjectId. Must be a 12-byte input or a 24-character hex string"}
        return jsonify(message), 400

    org = handle.orgs.find_one({"_id": orgId})

    if isAuthorized(requestorEmail, 'deleteOrg', orgId):
        # Cancel Stripe subscription
        if 'subscriptionId' in org:
            try:
                subscriptionId = org['subscriptionId']
                subscription = stripe.Subscription.retrieve(subscriptionId)
                subscription.delete()
            except Exception as e:
                errorDict = {"message": "Error occurred while updating existing subscription. Error message: {error}".format(error=e)}
                return jsonify(errorDict), 400

        # Delete all associated user accounts
        handle.users.remove({"orgId": orgId})

        # Delete all associated routes
        handle.routes.remove({"orgId": orgId})

        try:
            result = handle.orgs.remove({"_id": orgId})
            return '{"message": "Successfully deleted org"}'
        except Exception as e:
            errorDict = {"message": "Failed to delete org. Error message: {error}".format(error=e)}
            return jsonify(errorDict), 400
    else:
        # Not authorized
        errorDict = {"message": "You are not authorized to delete that org!"}
        return jsonify(errorDict), 403


@app.route("/orgs/<orgId>/subscription", methods=['PUT'])
@requires_auth
def addSubscriptionToOrg(orgId):
    orgId = validateObjectId(orgId)

    if not orgId:
        message = {"message": "Invalid org ID, uanble to convert to ObjectId. Must be a 12-byte input or a 24-character hex string"}
        return jsonify(message), 400

    # Check org for existing customer ID
    try:
        org = handle.orgs.find_one( {"_id": orgId} )
        if org:
            print "org is:"
            print org
            if 'subscriptionId' in org:
                try:
                    subscriptionId = org['subscriptionId']
                    subscription = stripe.Subscription.retrieve(subscriptionId)
                    subscription.plan = request.values['subscription']
                    subscription.save()
                except Exception as e:
                    errorDict = {"message": "Error occurred while updating existing subscription. Error message: {error}".format(error=e)}
                    return jsonify(errorDict), 400

                result = handle.orgs.update_one( {"_id": orgId},
                    {
                      "$set": {
                        "subscription": request.values['subscription']
                      }
                    }
                )
                message = {"message": "Org updated with subscription successfully"}
                return jsonify(message)
            else:
                try:
                    stripeCustomer = stripe.Customer.create(
                      source=request.values['tokenId'], # obtained from Stripe.js
                      plan=request.values['subscription'],
                      email=request.values['userEmail']
                    )
                    print "stripeCustomer is:"
                    print stripeCustomer
                    subscriptionId = stripeCustomer.subscriptions.data[0].id
                except Exception as e:
                    errorDict = {"message": "Error occurred while creating new user and subscription. Error message: {error}".format(error=e)}
                    return jsonify(errorDict), 400
                result = handle.orgs.update_one( {"_id": orgId},
                    {
                      "$set": {
                        "subscription": request.values['subscription'],
                        "subscriptionId": subscriptionId
                      }
                    }
                )
                message = {"message": "Org updated with subscription successfully"}
                return jsonify(message)
        else:
            message = {"message": "No org with that id"}
            return jsonify(message), 404
    except Exception as e:
        errorDict = {"message": "Failed to update org. Error message: {error}".format(error=e)}
        return jsonify(errorDict), 400

###################################
###   End of Org URIs
###################################



###################################
###   Subscription URIs
###################################

@app.route("/subscriptions/<subscriptionName>", methods=['GET'])
@requires_auth
def getSubscription(subscriptionName):
    subscription = handle.subscriptions.find_one({"subscriptionName": subscriptionName})
    if subscription:
        subscription['_id'] = str(subscription['_id'])
        return dumps(subscription)
    else:
        message = {"message": "No subscription found with that subscriptionName"}
        return jsonify(message), 404

###################################
###   End of Subscription URIs
###################################

###################################
###   Non-URI Functions
###################################

def validateObjectId(objectId):
    try:
        objectId = ObjectId(objectId)
        return objectId
    except:
        return None

def isAuthorized(actorEmail, action, target=None):
    actor = handle.users.find_one({"userEmail": actorEmail})
    org = handle.orgs.find_one({"_id": ObjectId(actor['orgId'])})
    print org
    maxRoutes = { 'freeTier': 1, 'developerTier': 10, 'enterpriseTier': 100 }
    if action == 'addUser':
        if (org['subscription'] == 'developerTier') or (org['subscription'] == 'enterpriseTier'):
            print 'Subscription is not freeTier, requesting user is authorized to add that user. Checking target org'
            if ObjectId(target) == actor['orgId']:
                print 'Target org matches requesting users org, request is authorized'
                return True
            else:
                return True
        else:
            print 'Subscription is freeTier or some unhandled value, requesting user is NOT authorized to add target user'
            return False

    elif action == 'deleteUser':
        targetUser = handle.users.find_one({"userEmail": target})
        if (org['subscription'] == 'developerTier') or (org['subscription'] == 'enterpriseTier'):
            if actorEmail != target:
                if actor['orgId'] == targetUser['orgId']:
                    print 'Subscription is not freeTier, the user is not deleting themself, and this user is part of the same org. Requesting user is authorized to delete target user'
                    return True
                else:
                    print 'Subscription is not freeTier, the user is not deleting themself, but the user is NOT part of the same org. Requesting user is NOT authorized to delete target user'
                    return False
            else:
                print 'Subscription is not freeTier, but the user is attempting to delete themself. Requesting user is NOT authorized to delete target user'
                return False
        else:
            print 'Subscription is freeTier or some unhandled value, requesting user is NOT authorized to delete target user'
            return False

    elif action == 'addRoute':
        routeCount = handle.routes.find({"orgId": org['_id']}).count()
        if routeCount < maxRoutes[org['subscription']]:
            print 'Current routeCount is less than maxRoutes, requesting user is authorized to create target route'
            return True
        else:
            print 'Current routeCount is equal to or greater than maxRoutes, requesting user is NOT authorized to create target route'
            return False
    elif action == 'deleteRoute':
        targetRoute = handle.routes.find_one({"incomingRoute": target})
        if org['_id'] == targetRoute['orgId']:
            print 'User is trying to delete a route that is owned by their org, requesting user is authorized to delete target route'
            return True
        else:
            print 'User is trying to delete a route that is NOT owned by their org, requesting user is NOT authorized to delete target route'
            return False
    elif action == 'deleteOrg':
        targetOrg = handle.orgs.find_one({"_id": ObjectId(target)})
        if org['_id'] == targetOrg['_id']:
            print 'User is trying to delete their own org, requesting user is authorized to delete target org'
            return True
        else:
            print 'User is trying to delete some other org, requesting user is NOT authorized to delete target org'
            return False
    else:
        print 'Unable to determine action, requesting user is NOT authorized'
        return False


###################################
###   End of Non-URI Functions
###################################


if __name__ == '__main__':
    app.run(app.config['IP'], app.config['PORT'])
```

If you want to see the rest of the files or fork my repo, you can find it on
GitHub:

[Flask Backend Repo](https://github.com/alexdglover/dnsreroute-api){: .btn .btn--info .btn--large}
{: .text-center}



## Redirecting Backend
The "dumb" redirect hosts are running a stripped down, simpler version of the
Flask app used by the API backend. The redirect hosts are part of an auto-scaling
group with an Elastic Load Balancer distributing requests. The configuration of
the hosts is all done through a userdata script, which makes auto-scaling simple
and removes the need for configuration management tools.

In my haste, I lumped together all of the code for the redirecting app and the
CloudFormation code into one repo. Check out the repo here:

[Redirecting Backend Repo](https://github.com/alexdglover/dnsreroute-aws){: .btn .btn--info .btn--large}
{: .text-center}

The Flask app for handling redirects is pretty straightforward. All of the
relevant logic is written in the redirectorApp.py file:

```python
import os
from flask import Flask, request, flash, url_for, redirect, \
     render_template, abort, send_from_directory, jsonify, _request_ctx_stack
import requests
from werkzeug.local import LocalProxy
from flask.ext.cors import CORS, cross_origin
import pymongo
from pymongo import MongoClient, IndexModel, ASCENDING, DESCENDING, errors


app = Flask(__name__)
app.config.from_pyfile('flaskapp.cfg')
CORS(app)


def connect():
    print os.environ['OPENSHIFT_MONGODB_DB_URL']
    print int(os.environ['OPENSHIFT_MONGODB_DB_PORT'])
    print os.environ['OPENSHIFT_MONGODB_DB_USERNAME']
    print os.environ['OPENSHIFT_MONGODB_DB_PASSWORD']
    connection = MongoClient(os.environ['OPENSHIFT_MONGODB_DB_URL'],int(os.environ['OPENSHIFT_MONGODB_DB_PORT']))
    handle = connection["dnsreroute"]
    handle.authenticate(os.environ['OPENSHIFT_MONGODB_DB_USERNAME'],os.environ['OPENSHIFT_MONGODB_DB_PASSWORD'])
    return handle

handle = connect()

# Controllers API
@app.route("/")
def home():
    host = request.headers['Host']
    host = host.split(':')[0]
    if host != 'service.dnsreroute.xyz':
        route = handle.routes.find_one({'incomingRoute': host})
        if route:
            outgoingRoute = route['outgoingRoute']
            if route['type'] == "301":
                return redirect(outgoingRoute, 301)
            elif route['type'] == "302":
                return redirect(outgoingRoute)
            else:
                return '{"message": "Error - not able to determine redirect type"}'
        else:
            return '{"message": "Could not find a matching route"}', 404

    else:
      return "The Host header is {hostHeader}.This is the unsecured home page".format(hostHeader=request.headers['Host'])

# Controllers API
@app.route("/health")
def healthCheck():
    return '{"message": "System OK!"}'


if __name__ == '__main__':
    app.run(app.config['IP'], app.config['PORT'])
```

One implementation note - I was originally using the `eventlet` Gunicorn worker
type, but as I was writing this blog post and testing things were still working
I discovered that my instances were no longer bootstrapping successfully. Turns
out it was some gunicorn/eventlet/monotonic error throwing a RuntimeError of
`no suitable implementation for this system`. Rather than go down that rabbit
hole, I just switched the worker type to `gevent` and moved on.


## Conclusion
I'll be leaving DNSReRoute running at least until July 2017, after which my 12 month
free trial of AWS expires. If I haven't stirred up any interest or customers by
then, I'll terminate the redirecting hosts, but keep the API backend and front-end
running for reference purposes.

I had a lot of fun writing this app, and I'm proud of it in spite of its flaws
and lack of use. I hope you were able to cherry pick some code or ideas from
this post, or some inspiration if nothing else.

Thanks for stopping by.
