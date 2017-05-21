---
layout: post
title:  "Meteor publications with pagination"
date:   2016-11-16 11:00:29 +0100
categories: Meteor React
---

Based on `https://medium.com/modern-user-interfaces/how-we-redux-part-3-domain-890964824fec#.ma2a7zckh`


Client will subscribe to all documents published. All logic behind which documents are published will be handled by the publication `Meteor.publish()` and filters/page passed to the publications using methods.


1. Create a new collection:

```

import { Mongo } from 'meteor/mongo';
export const Activities = new Mongo.Collection('activities');

```

2. Create filtered and paginated publication

```
const getActivitiesPublication = (filter, pageSkip = 0) => {
  switch (filter) {
    case 'SHOW_ALL':
      return Activities.find({}, { skip: pageSkip, limit: 10 });
    default:
      return Activities.find({}, { skip: pageSkip, limit: 10 });
  }
};


// publish per page
Meteor.publish('getActivities', getActivitiesPublication);
```

3. Publish the count of publications in the published cursor

`meteor add tmeasday:publish-counts`

4. Client 2 Server communication happens via *Methods*
