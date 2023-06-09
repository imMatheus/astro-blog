---
title: '5 Improvement ideas for Firebase'
description: ''
pubDate: 'Jan 08 2023'
updatedAt: 'Jul 13 2022'
heroImage: '/placeholder-hero.jpg'
---

## 1. Projcetion

Projection is a common feature in the vast majority of popular databases such as MongoDB and PostgreSQL that gives the option to only retrieve some fields from a document. This has two main crucial benefits, firstly it allows you to decrease the number of data that gets transferred to the client which would be very beneficial if you have a big user base that is mobile. Only selecting the fields you want would give a better user experience. The second reason projection is super useful is that this could be paired with a new rule in your security rules that would not allow certain fields to be retrieved in some scenarios. For example, let us say that you have a collection of users where every user could choose their profile to be public or private, if a user is public all fields should be accessible but if the user is private the email fields should not be accessible. This behavior is not possible at the moment in firestore, and the recommended way to mimic this behavior from the firestore documentation is to split up your data into two different documents which is a big pain to deal with and maintain.

Here is an example of how the code for the javascript client SDK might look:

```js
// example.js
import { collection, query, getDocs, projection } from 'firebase/firestore'
import { db } from './firebase'

const q = query(collection(db, 'users'), projection({ name: 1, age: 1 }))
const querySnapshot = await getDocs(q)
```

Here is an example of how the syntax for only accessing certain fields in your security rules might look:

```js
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId} {
      allow read: if resource.data.visibility == 'private' &&
                  ['email'] notin request.resource.data.retriviedKeys();
    }
  }
}
```

## 2. Where queries in security rules

The security rules of firestore are great in the majority of ways but one thing I have found is that it is not possible to perform where queries. To find if a document exists that has a certain value. This would be very nice to have for making sure that a value is unique, for example, let us say that you have a collection of **users** and each user must have a unique **username**, I have yet to find a nice and easy way of doing this.

Here is an example of how the syntax for this in your security rules might look:

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId} {
      allow write: if !exists(/databases/$(database)/documents/users, where('username', '==', request.resource.data.username));
    }
  }
}
```

## 3. Security rules formatter

This is pretty straightforward, as I write this I have yet to find a tool that can format my security rules and I would love to have it as it can get a bit messy with unformatted rules. This would just be a matter of adding a button to the navbar (as shown in the image down below) which would format it.

<img src="/tes.png" alt="security rules formatter" />

## 4. Count number of documents in a collection

The ability to count the number of documents in a collection is a feature that I don't get how it can not be a thing. I am not sure how other databases like MongoDB do it, but a relatively straightforward implentaion would be to take the documents retrieved after the query and just return the array length instead of the array of documents. This way the amount of data sent to the client would be drastically minified. It may not be the best solution but at least it's a feature.

Here is an example of how the syntax for this might look:

```js
import { collection, query, countDocs } from 'firebase/firestore'
import { db } from './firebase'

const q = query(collection(db, 'users'))
const querySnapshot = await countDocs(q)
```

## 5. Full text search

I find it a bit obscured that Firebase, maintained by Google which is the world's biggest search engine and also owns youtube the world's second-biggest search engine, still doesn't have a native way to search thru your documents. Not much more to say here, just please Firebase introduce something to enable the developers to search thru the documents.
