---
layout: article
title: "MeteorJS, iron-router and Bootstrap 3 - open a modal with its own URL"
categories: code
comments: true
ads: true
image:
  teaser: meteor-modal-teaser.png
---

## Why open a modal with its own URL?

Just to set the scene, I am currently using [MeteorJS](https://www.meteor.com/) with [iron-router](https://github.com/iron-meteor/iron-router) and [bootstrap 3](http://getbootstrap.com/), and I am using [bootstrap modals](http://getbootstrap.com/javascript/#modals) when creating new data and editing existing data.

My philosophy is that every page we open should have its open URL, modal or none modal.  That means if we refresh a page that has a modal, the modal will open automatically.

Conversely, modals that don't have their own URL will not appear when we refresh a screen that had an open modal.

## The Bootstrap approach

Lets begin by reviewing the bootstrap approach to modals.  Imagine we are on a web page containing a list of contacts: the contacts **index** (or **list**) page.  We click one of the contacts and access that particular contact's **show** page.

At this point we decide to edit the contacts data so we click an **edit contact** link and our modal is opened with a form containing the data that can be edited.

The bootstrap approach will see that the **edit** link has been pressed and, using some javascript built into bootstrap, the modal will be opened.  However, the the URL will remain unchanged.

## MeteorJS and iron-router

So how do we update URLs as modals open and close?  Well, this is where the awesomeness of [iron-router](https://github.com/iron-meteor/iron-router) comes into play.

Iron-router allows us to do three (3) important things for each of our URLs:

1. Determine the layout of the page (i.e. select the Meteor templates to be rendered);
2. Select and prepare data to be rendered in the layout (carry out data subscriptions and any data manipulation or preparation before any data is rendered to a page); and,
3. Perform any other actions that are required before or after rendering a page and its data.

### Show Page: Rendering

Firstly, lets go back to the example of the contacts web pages we discussed initially.  Again lets assume we have arrived at the page showing the details of one particular contact (the contact **show page**).

Here we need to be able to view the various data of the contact.  Also, since we will be opening a modal from this page we must load the modal's HTML into this **show page**. However, the modal will be in a closed state (not open).

#### 1. Show Page: Templates

So, in terms of iron-router, our **routes.js** file for the templates will look something like this:

{% highlight javascript %}
Router.route('/contacts/:_id', {
  name: 'contacts.show',

  layoutTemplate: 'layout',

  template: 'contactShow',
  yieldTemplates: {
    'editContactModal': { to: 'modal' },
  },

  ...

});
{% endhighlight %}

In this, we have reference to out **layout** file, e.g.:

{% highlight jade %}
template(name="layout")

  ....

  #content
    +yield
    +yield region="modal"

  ...

{% endhighlight %}

**contactShow** template is rendered into the **+yield**, which is the layout of the **show page**. **editContactModal** template is rendered into **+yield region=modal**, which is the closed modal.

#### 2. Show Page: Subscriptions and Data Preparation

Next we use the **waitOn** section of iron-router to subscribe to the contacts data and the **data** section to find the one record required to render into the **show page** template:

{% highlight javascript %}
Router.route('/contacts/:_id', {

  ...

  waitOn: function () {
    return Meteor.subscribe('contacts')
  },

  data: function () {
    Session.set('contact_id', this.params._id);

    var contact = Contacts.findOne({ ... });
    return {
      contact: contact
    }
  }

  ...

});
{% endhighlight %}

Note: we also set a **session** for the contact being shown.  The **_id** will be used later when we need to edit this contact.

#### 3. Show Page: Actions

Finally, we have the option to carry out any actions (hooks) before, during or after rendering.  Assuming we simply want to render out data into the templates, we can call the following, which will render the data into the templates:

{% highlight javascript %}
Router.route('/contacts/:_id', {

  ...

  action: function () {
    this.render();
  },

  onStop: function () {
    delete Session.keys.contactId;
  }

  ...

});
{% endhighlight %}

Note: this is also set up to delete the contact's **_id** stored in a session when we move away from the **show page** as it will no longer be required.

### Modal Edit Page: Rendering

Now this is where the beauty of using iron-router and the opening and closing of modals really comes through. So, here are points 1-3 for the **modal edit page** (the **modal edit URL**).

#### 1. Modal Edit Page: Meteor Templates

So at this point we must remember that to open a modal, as it opens, a backdrop obscures the **show page** behind and the modal sits on top of the everything.

Now in the case of our contacts, as the **edit modal** is opened, we still want to see the **show page** behind the modal.  As such, the templates of the **contacts.edit** route must be an exact copy of the **contacts.show** route, as follows:

{% highlight javascript %}
Router.route('/contacts/:_id/edit', {
  name: 'contacts.edit',

  layoutTemplate: 'layout',

  template: 'contactShow',
  yieldTemplates: {
    'editContactModal': { to: 'modal' },
  },

  ...

});
{% endhighlight %}

#### 2. Modal Edit Page: Subscriptions and Data Preparation

Again, since we still need to render the **show page** behind the modal with exactly the same data, the **waitOn** and **data** sections of the **edit page** must be at least the same as the **show page**, as follows:

{% highlight javascript %}
Router.route('/contacts/:_id/edit', {

  ...

  waitOn: function () {
    return Meteor.subscribe('contacts')
  },

  data: function () {
    Session.set('contact_id', this.params._id);

    var contact = Contacts.findOne({ ... });
    return {
      contact: contact
    }
  }

  ...

});
{% endhighlight %}

Hence, the same data is being shown on the obscured **show page** behind the modal, and this data is also used in the **edit modal**.

#### 3. Modal Edit Page: Actions

Now point 3 is the only part that must be different compared to the **show page**.  It is with the **actions** that we can open and close the modal. We use the **action** to open the modal and the **onStop** action to close the modal, as follows:

{% highlight javascript %}
Router.route('/contacts/:_id/edit', {

  ...

  action: function () {
    $('#editContactModal').modal('show');
    this.render();
  },

  onStop: function () {
    $( '#editContactModal' ).modal('hide');
    delete Session.keys.contactId;
  }

  ...

});
{% endhighlight %}

> Note: I reference the modal through its own id, in this case the id is called **editContactModal**

Taking this approach, as we render the **edit page**, the modal is opened, and when we leave the **edit page**, such as when we submit the modal or cancel the modal, the **onStop** action of this route will close the modal.

### Additional Functionality

The above describes the basic routing of this approach. However, in addition to the iron-router ensuring the correct templates and data are rendered, we are missing the functions to direct us between pages (the on-page links or buttons).

In order to open the **edit page** from the **show page**, we can implement a simple **href link** or we can **data-bind** a button or link, an example being as follows:

{% highlight javascript %}
Template.contactShow.events({
  'click [data-bind="editContact"]': function () {
    Router.go('contacts.edit', { _id: Session.get('contactId') });
  }
});
{% endhighlight %}

> Note: we utilise the contact's **_id**, stored in the **contactId session** created in the route (iron-router), to access the **edit page**.

The same goes for when we are in the **edit page**, when we close the modal we need to be returned to the **show page**, as follows:

{% highlight javascript %}
Template.contactModalEdit.events({
  'submit form#editContactForm': function () {
    Router.go('contacts.show', { _id: Session.get('contactId') });
  },
  'click form#editContactForm .close-modal': function () {
    Router.go('contacts.show', { _id: Session.get('contactId') });
  },
  'click #editContactModal .modal-backdrop': function () {
    Router.go('contacts.show', { _id: Session.get('contactId') });
  }
});
{% endhighlight %}

This allows the modal to be closed by either submitting the edit form of the modal, pressing the modals cancel button or by clicking on the **modal-backdrop** (bootstraps css id of the backdrop that obscures the page behind a modal), and redirects to the **show page**.

## Final Interesting Point

Now you may be thinking to yourself, when we open the **modal edit page** and we are also rendering the **show page** behind it at the same time, this must surely slow things down when moving to the **edit URL**.  Surprisingly enough no.  Meteor is clever enough to know that it has already rendered the **show page** and since data has not changed for the **show page**, it will not completely repeat the rendering when you move to the **edit page**.

Meteor is simply amazing and iron-router makes it all that much better!
