---
layout: defaults
title: Tidepool Platform Roadmap
published: true
---

This document was last edited at the start of April, 2015.

# Platform Roadmap

This document lays out a roadmap of Tidepool's general technical focus for our platform the next several months. It is not a product design document, and specifically does not cover the design or development of our user-facing features within apps such as Blip.

## Overview

Tidepool's team is small. We are constrained by our available resources, and we are also constrained by various external factors. Our primary, long-term goal is to have the maximum possible positive impact on people who have Type 1 Diabetes. We believe that means that we should concentrate on getting our first bit of software into the hands of real users.


From a corporate point of view, this means that we must:

  1. Get usability feedback from clinicians and patients and iterate so Blip is ready for primetime.
  1. Create a user-friendly uploader that gets device data into Tidepool's cloud.
  1. Build a platform to host data so we can sell hosting services.
  1. Implement a quality system that satisfies FDA requirements.
  1. Submit for (and then receive) FDA approval.

Our very aggressive goal is to submit the appropriate application to the FDA by the end of 2014. Update as of October, 2014: We met with the FDA and received guidance (notes [here](https://tidepool.org/wp-content/uploads/2013/08/002_Tidepool_Meeting_Minutes.pdf)) that we should Register and List, do not need to submit a 510(k). So we are still building a quality system and implementing within it, but documenting is internal, not to be filed.

As mentioned above, this document does not consider the design and development of user interfaces. It is focused on getting a useful, robust, HIPAA-compliant backend built, tested, deployed, and capable of being FDA-approved.

It is these ends that drive the prioritization and sequencing of the items in this document.

Items marked with *@@* are lower priority given our current company priorities. Some of these (at least the ones not tied to Tidepool's deployment) might be good candidates for third party contributors to consider working on, as it's less likely that Tidepool's developers will be working on them soon. These items are marked with *@@3*.

## Our technical goals

These are the high level technical goals that are informed by our company goals. The order in which they're listed is definitely not the order in which they will be implemented. Note that these are the high level goals; details are later in the document.

* *Make Blip's feature set commercially viable.*  Our initial implementation of Blip, currently undergoing its first medical trial, is lacking a few important features that will have to exist before we can make it available to a larger population. For a general outline, see below. For specific details on exactly what we're working on, please visit [our Blip Trello board](https://trello.com/b/GPadCYvP/blip). That board will be continually updated to reflect our current status and intentions.
* *Build a "Universal Uploader".* We believe that extracting data from third party sites that have already processed and filtered it is the wrong way to collect data. We want to build interfaces that will allow Tidepool's platform to extract data directly from devices like pumps and CGMs. We intend to do this by working directly with the manufacturers of these devices.
* *Continue to extend the Tidepool platform for third party uses.* Tidepool intends to be a hosting provider for data related to diabetes -- not just device data, but all the data that has an effect on blood sugar or insulin dosing decisions (exercise, food intake, etc). We also believe that we promote the best possible outcomes by being open about the entire platform, and by hosting data for anyone who chooses to work with us.
* *Build a world-class quality system.* We recognize that the work we're doing directly affects people's health. We must be very, very confident in the code we write and how we deploy it. But the traditional approach to life-safety software development uses a "design-first" approach that attempts to specify every requirement in advance. It's been shown time and again that that approach leads to massive cost overruns and explodes the size of development teams and the time required to build a solution. At Tidepool, we believe that we can show the world how to build great, high-quality software with a small team in an agile way. To prove it, we will need a concerted approach to how we manage all elements of quality -- from documentation to testing to verification to validation to requirements traceability.

## Steps to get there:

Below are the major areas we are working towards to achieve the goals set out above. There is some sequence to this list of steps, but we will certainly not follow this sequence exactly. There are too many variables and people to impose significant control over sequencing.

### Provide a full spectrum of standard platform capabilities

Our platform needs to be a world-class, documented, secure, tested framework of capabilities for storing and retrieving diabetes data. Certain things are required from anything that wants to call itself a platform.

Descriptions here will usually be terse -- fuller explanations of what they mean can usually be found in GitHub or our Trello boards; if you want to know more about something and can't find it here, ask us in our IRC channel and someone will help you find the right place. For all these links, see [the main page](http://tidepool-org.github.io/).

#### For our own needs:

These are API features that we need in order to support Blip in the next few months.

  * *@@* Develop an "undergoing maintenance" page that we can quickly failover to if there are technical problems so that users understand why things are failing.
  * *@@* Develop a framework that better allows for partial deploys and A/B testing. Our current implementation of [hakken and styx](/server-architecture) allow for a fair amount of flexibility here, but addition of a few capabilities (such as the ability to direct a certain percentage of users to a given deployment) would give us more control.

#### Make our apps into "third party" apps:

These features are aimed at allowing us to support third parties -- people who want to use Tidepool's platform as a backend to their own applications, or open source people who want to stand up their own versions of Tidepool's APIs. Tidepool's client apps will not be privileged in their access to security information. This is not required for commercialization but it is next in line.

  * Create API token system so we can let other apps use the platform.
  * Support OAuth2 and provide a platform login page so we can let users control which apps use the platform.
  * Support a third-party-accessible sandboxed version of our API so that app developers can build against a dummy API without real customer data. Provide some test accounts or test account generators so that people can experiment without creating lots of bogus data in our production database.

#### For privacy / security / data stewardship:

We believe strongly that people own their own data and need to finish the work that will allow people to control their data. We need to follow security best practices and defend our systems against attacks and poor programming.

  * Provide standalone endpoints for managing things that are central to the platform and should be common across multiple apps:
    * Login (this is the point of OAuth support)
    * Signup
    * User profile management (including invitations, editing contact information and password recovery)
  * Throttle API calls -- especially to the user-api -- to limit ability of badly-behaved clients to damage us.
  * Add password quality rules so people can't create unsafe passwords.
  * Export: allow downloading all of someone's data in one action (result should be something that is machine-interpretable, but that can be understood with a little effort by someone who is not a software developer -- for example, a zip of JSON files).
  * *@@* Import: allow upload of one of these data blobs to recreate an individual's data.
  * *@@* Complete the features involved in allowing someone to delete their own account and all associated data.
  * *@@* Log database transactions so we can play them back for recovery between daily backups. This permits backup recovery without losing any data. (Requires ability to play back a log and feed it into the database.)


#### Architectural thinking we need to do first:

  * Design and document our unified data storage model:
    * How do we handle queries? We probably need a small query language. Design something that can start small and be extended.
    * How will we do notifications based on those queries? (In this case, "notifications" means a mechanism for software systems to be notified when items in the database are changed according to certain conditions.)

#### Implementation that enables future needs:

  * Create a structure for a more general query system that will work not only for Blip but for a variety of needs.
  * Design tool for adjusting timestamps and time zones on blocks of data
  * Build tool for adjusting timestamps and time zones on blocks of data (possibly extend query system to support it). Make sure it's tracked in the provenance system.
    * It likely requires UI design and prototyping and lots of user testing, plus frontend and backend coding and probably several iterations.
  * Design and build a notification system that allows apps to subscribe to changes based on stored queries. It should support both connected and disconnected notifications (so, for example, someone could set an alert to notify an app when new data is uploaded to an account).

### Universal Uploader

  * *@@3* Implement mobile app that can use a USB/Bluetooth converter to upload data from a device in real time.

### Step up test coverage, documentation and process

As discussed above, we need to be very serious about testing and measurement of that testing.

  * Continue to increase the number of tests in our deployed backend APIs; basically, each API and support library should have a suite of tests that is reasonably likely to catch regressions if code is edited.
  * Get all active backend repositories covered by Travis-CI so that tests run automatically on every checkin.
  * Start using code coverage tools to measure code coverage. Develop some rules of thumb to decide when we have "enough"? Hook that up to everything after Travis so that we have measurements.
  * Start analyzing our dependencies and their testing coverage; decide how to manage them in a way we can track. Minimize dependencies and stabilize versions.
  * Re-engage on our test strategies for the frontend. Automated end-to-end testing is probably not the most effective use of our resources. How can we do this better?
  * Get further documentation set up and figure out how to make sure it's current and correct. The right way to do it is to find a way to integrate the documentation into the code ([JSDoc](http://usejsdoc.org/)?) so that it can be generated directly from the code.

