# COMP5216 Mobile Computing

# 2024 S2

# Lab 04 – Cloud Service

**Objectives:**
1. Learn how to develop a mobile app using Google’s Firebase platform
2. Understand how to use Cloud Firestore to store and read data

**Tasks:**
1. Connect the app to Firebase and configure Cloud Firestore
2. Write data to Cloud Firestore
3. Display data from Cloud Firestore
4. Sort and filter data in Cloud Firestore

# School of Computer Science

# Page 1 of 13

# COMP5216 Mobile Computing

# LabW04

We have so far learnt how a mobile app persists data locally on the phone (refer Lab Week 03). It does not allow data access from other clients, such as another app or website.

This tutorial is adapted from Cloud Firestore Android Codelab and shows you how to connect a mobile app to Cloud Firestore – Google’s Firebase NoSQL Database in the cloud for mobile and web apps – and then query data from it. You will learn how to build a restaurant recommendation app powered by Cloud Firestore called “Friendly Eats”. In addition, you will also learn how to:
• Connect your app to Google’s Firebase platform and configure Cloud Firestore.
• Read and write data to Cloud Firestore from an Android app.
• Listen to changes in Firestore data in real-time.
• Use basic Firebase Authentication

Note: this tutorial uses the free Spark plan, which is sufficient for development purposes for most apps.

**Task 1: Connect app to Firebase and configure Cloud Firestore**
1. Sign into the Firebase console with your Google account.
2. On the Firebase console, click Add project.
Your Firebase projects
Add project
3. In the “Create a project (Step 1 of 3)”, enter a name for your Firebase project, for example, "Friendly Eats". Click Continue.
4. In step 2 of 3 “Google Analytics for your Firebase project”, choose “Enable Google Analytics for this project”. Click Continue.
5. In step 3 of 3 “Configure Google Analytics”, choose “Create a new account”, enter “COMP5216” as the new Google Analytics account name. Click Save.
6. Leave the option for “Analytics location” as “United States” and leave the default option for “Use the default settings for sharing Google Analytics data.”
7. Accept the Google Analytics terms.
8. Click “Create project”.
9. After a minute or so, your Firebase project will be ready. Click Continue.
10. Download from Canvas and unzip the sample base code provided for Fire Eats app: “friendlyeats - android.zip”. Once unzipped, a folder “friendlyeats - android” should be created on your machine. You can also download the sample code directly from GitHub.

# School of Computer Science

# Page 2 of 13

# COMP5216 Mobile Computing

# LabW04
11. Import the project into Android Studio. You will probably see some compilation errors or maybe a warning about a missing google - services.json file.
12. On the Firebase console, select Project Overview in the left navigation. Get started by adding Firebase to your app by clicking the Android button to select the platform. When prompted for an Android package name use “com.google.firebase.example.fireeats”.
Get started by adding Firebase to your app
iOS
Add an app to get started
13. Click “Register app” and follow the instructions to download the “google - services.json” config file, and move it into the app/ folder of your Android app code. Click Next.
14. Follow the instructions to add the Firebase SDK dependencies to your gradle files by modifying your build.gradle files to use the Google services plugins:
• Project - level build.gradle (<project>/build.gradle)
buildscript {
dependencies {
// Add this line
classpath 'com.android.tools.build:gradle:8.5.0'
classpath 'com.google.gms:google - services:4.4.2'
• App - level build.gradle (<project>/<app - module>/build.gradle):
apply plugin: 'com.google.gms.google - services' // Add this line
dependencies {
// Add this line
// Import the Firebase BoM implementation platform('com.google.firebase:firebase - bom: 33.1.2')
// When using the BoM, don't specify versions in Firebase dependencies
implementation 'com.google.firebase:firebase - analytics'
Gradle files have changed sine last prpietyr prjict yrc ma be essay r t uor prprt. Sync Now
15. Finally, press "Sync now" in the bar that appears in Android Studio. Click Next.
16. Run the app to verify installation. You should see the below message on the Firebase console if Firebase is successfully added to your app.

Congratulations, you've successfully added Firebase to your app!

# School of Computer Science

# Page 3 of 13

# COMP5216 Mobile Computing

# LabW04
17. Next, we should set some basic rules to restrict data access to users who are signed in, so that we can prevent unauthenticated users from reading or writing. Under “Product categories -> Build" select "Authentication". Then,"Get started" -> "Sign - in method" Tab -> select “Email/Password”.
Email/Password Enable
Allow users to sign up using their email address and password. Our SDKs also
provide email address verifcation, password recover, and email adress hange
primitives. Learn more
Email link(passwordless sign - in) Enable
18. Enable Cloud Firestore for your project Under “Product categories -> Build” select “Firestore Database” -> “Create database” to provision Cloud Firestore database.
19. Set a location for your Cloud Firestore as “nam5 (us - central)”. Once the location is set, you cannot change it later. To learn more, you can read Select locations for your project.
20. Select a starting mode for your Cloud Firestore secure rules. Choose “Start in test mode” to get set up quickly by allowing all reads and writes to your database. Click Next.
21. Click Enable to provision Cloud Firestore.
….
22. Access to data in Cloud Firestore is controlled by Security Rules. First we need to set some basic rules on our data to restrict access only to users who are signed in. In the console, navigate to "Cloud Firestore" -> "Rules" tab, add these rules and click Publish:
service cloud.firestore {
match /databases/{database}/documents {
match /{document = **} {
allow read, write: if request.auth!= null;
23. If you have set up your app correctly, the project should now compile. In Android Studio click Build -> Rebuild Project. Run the app on your Android device. At first you will be presented with a "Sign in" screen. You can use an email and password to sign up into the app. Once you have completed the sign in process you should see the home screen:

# School of Computer Science

# Page 4 of 13

# COMP5216 Mobile Computing

# LabW04
Y1 Friendly Eats

**Task 2: Write data to Cloud Firestore**
In this task, we will write some data to Cloud Firestore so that we can populate the currently empty home screen. You can enter data manually in the Firebase console, but we'll do it in the app itself to demonstrate how to write data to Firestore using the Android SDK.

The main model object in our app is a restaurant (refer model/Restaurant.java). Firestore data is split into documents, collections, and subcollections. We will store each restaurant as a document in a top - level collection called "restaurants". To learn more about the Firestore data model, read about documents and collections.
1. First, let's get an instance of FirebaseFirestore to work with. Edit the initFirestore() method in MainActivity:
private void initFirestore() {
mFirestore = FirebaseFirestore.getInstance();

# School of Computer Science

# Page 5 of 13

# COMP5216 Mobile Computing

# LabW04
2. Add functionality in the app to create 10 random restaurants when we click the "Add Random Items" button in the overflow menu. Fill in the onAddItemsClicked() method:
private void onAddItemsClicked() {
// Get a reference to the restaurants collection
CollectionReference restaurants = mFirestore.collection("restaurants");
for (int i = 0; i < 10; i++) {
// Get a random Restaurant POJO Restaurant restaurant = RestaurantUtil.getRandom(this);
// Add a new document to the restaurants collection
restaurants.add(restaurant);
There are a few important things to note about the code above:
• We started by getting a reference to the "restaurants" collection. Collections are created implicitly when documents are added, so there was no need to create the collection before writing data.
• Documents can be created using Plain Old Java Objects (POJOs), which we use to create each Restaurant doc.
• The add() method adds a document to a collection with an auto - generated ID, so we did not need to specify a unique ID for each Restaurant.
3. Now run the app again and click the "Add Random Items" button in the overflow menu to invoke the code you just wrote:
4
Sign Out
Add Random Items
If you navigate to the Firebase console -> "Firestore Database -> "Data" tab,you should see the newly added data as per below. Congratulations, you just wrote data to Cloud Firestore! In the next step we'll learn how to display this data in the app.

# School of Computer Science

# Page 6 of 13

# COMP5216 Mobile Computing

# LabW04
restaurants>7Ay32c3kQ7Vo...
friendly - eats - 8845b restaurants 7Ay32c3kQ7VodozVqpDC 1
Start collectior Add document Start collection
restaurants 7Ay32c3kQ7VodozVapDC Add field
FKEi5VyHFSSK7TDOHN11 Cz2eE2NXhrntUvEYOXBz avgRating:3.072849915035425 category:"Indian
I2e5AbgvgCW7wV8Bjx8S K8TY6Php03epGn6bgP1m KULBcBPKEOv1KtOr6zz3 city:"Indianapolis name:"Fire Eatery" numRatings:13
Z0gf6e0CwBcAgu855KGK photo:https://storage.googleapis.com/firestorequickstart
juaMWaDPWJzIxnmaX1of price:3
t9Y61uDfIGGhzKXYLXjz
zJt16mY2F83v2upnRU3

**Task 3: Display data from Cloud Firestore**
In this task we will learn how to retrieve data from Cloud Firestore and display it in our app.
1. The first step to reading data from Cloud Firestore is to create a Query. Modify the initFirestore() method:
private void initFirestore() {
mFirestore = FirebaseFirestore.getInstance();
mQuery = mFirestore.collection("restaurants") // Get the 50 highest rated restaurants
.orderBy("avgRating", Query.Direction.DESCENDING
.limit(LIMIT);
2. Now we want to listen to the query, so that we get all matching documents and are notified of future updates in real time. Because our eventual goal is to bind this data to a RecyclerView, we need to create a RecyclerView.Adapter class to listen to the data. Whilst this would demonstrate the real - time capabilities of Cloud Firestore, but it is also simple to fetch data without a listener. You can call get() on any query or reference to fetch a data snapshot.
Open the FirestoreAdapter class, which has already been partially implemented. First, let's make the adapter implement EventListener and define the onEvent function so that it can receive updates to a Firestore query:

# School of Computer Science

# Page 7 of 13

# COMP5216 Mobile Computing

# LabW04
public abstract class FirestoreAdapter<VH extends RecyclerView.ViewHolder>
extends RecyclerView.Adapter<VH>{
//...
public void onEvent(QuerySnapshot documentSnapshots,
FirebaseFirestoreException e) {
if // Handle errors (e!= null) {
Log.w(TAG, "onEvent:error", e);
return;
}
// Dispatch the event
for (DocumentChange change : documentSnapshots.getDocumentChanges()) {
// Snapshot of the changed document DocumentSnapshot snapshot = change.getDocument();
switch (change.getType()) {
case ADDED:
// TODO: handle document added
break;
case MODIFIED:
break; // TODO: handle document modified
case REMOVED:
// TODO: handle document removed
break;
}
onDataChanged();
3. On initial load the listener will receive one ADDED event for each new document. As the result set of the query changes over time the listener will receive more events containing the changes. Now let's finish implementing the listener. First add three new methods: onDocumentAdded, onDocumentModified, and onDocumentRemoved:
protected void onDocumentAdded(DocumentChange change) {
mSnapshots.add(change.getNewIndex(), change.getDocument());
notifyItemInserted(change.getNewIndex());
protected void onDocumentModified(DocumentChange change) {
if (change.getOldIndex() == change.getNewIndex()) {
// Item changed but remained in same position
mSnapshots.set(change.getOldIndex(), change.getDocument());
notifyItemChanged(change.getOldIndex());
} else {
// Item changed and changed position
mSnapshots.remove(change.getOldIndex());
mSnapshots.add(change.getNewIndex(), change.getDocument());
notifyItemMoved(change.getOldIndex(), change.getNewIndex());
}
protected void onDocumentRemoved(DocumentChange change) {
mSnapshots.remove(change.getOldIndex());
notifyItemRemoved(change.getOldIndex());

# School of Computer Science

# Page 8 of 13

# COMP5216 Mobile Computing

# LabW04
4. Call the three new methods from onEvent:
@Override
public void onEvent(QuerySnapshot documentSnapshots,
FirebaseFirestoreException e) {
if (e!= null // Handle errors
Log.w(TAG, "onEvent:error", e);
return;
}
// Dispatch the event
for (DocumentChange change : documentSnapshots.getDocumentChanges()) {
// Snapshot of the changed document
DocumentSnapshot snapshot = change.getDocument();
switch (change.getType()) {
case ADDED:
onDocumentAdded(change);
break;
case MODIFIED:
onDocumentModified(change);
break;
case REMOVED:
onDocumentRemoved(change);
break;
}
onDataChanged();
public void startListening() {
5. Finally implement the startListening() method to attach the listener:
if (mQuery!= null && mRegistration == null) {
mRegistration = mQuery.addSnapshotListener(this);
6. Now the app is fully configured to read data from Cloud
Firestore. Run the app again and you should see the restaurants you added in the previous step.
Go back to the Firebase console and edit one of the restaurant names. You should see it change in the app almost instantly!

# School of Computer Science

# Y1 Friendly Eats

# AIR

# Page 9 of 13

# COMP5216 Mobile Computing

# LabW04

**Task 4: Sort and filter data in Cloud Firestore**
The app currently displays the top - rated restaurants across the entire collection, but in a real restaurant app the user would want to sort and filter the data. For example, the app should be able to show "Top seafood restaurants in New York" or "Least expensive pizza".

Clicking white bar at the top of the app brings up a filters dialog. In this section we'll use Firestore queries to make this dialog work:
Y1Friendly Eats
Filter
All food
Any price
Sort by Rating
APPLY
1. Edit the onFilter() method of MainActivity.java. This method accepts a Filters object, a helper object we created to capture the output of the filters dialog. We will change this method to construct a query from the filters.
In the code snippet below, we build a Query object by attaching where and orderBy clauses to match the given filters.

# School of Computer Science

# Page 10 of 13

# COMP5216 Mobile Computing

# LabW04
@Override
public void onFilter(Filters filters) {
// Construct query basic query
Query query = mFirestore.collection("restaurants");
if (filters.hasCategory()) { // Category (equality filter)
query = query.whereEqualTo("category", filters.getCategory());
}
if // City (equality filter) (filters.hasCity()) {
query = query.whereEqualTo("city", filters.getCity());
}
// Price (equality filter)
if (filters.hasPrice()) {
query = query.whereEqualTo("price", filters.getPrice());
}
// Sort by (orderBy with direction)
if (filters.hasSortBy()) {
query = query.orderBy(filters.getSortBy(), filters.getSortDirection());
}
// Limit items
query = query.limit(LIMIT);
mQuery = query;
mAdapter.setQuery(query); // Update the query
// Set header
mCurrentSearchView.setText(Html.fromHtml(filters.getSearchDescription(this)));
mCurrentSortByView.setText(filters.getOrderDescription(this));
mViewModel.setFilters(filters); // Save filters
2. Run the app and select the following filter to show the most popular low - price restaurants:
Filter
All food
Anywhere
S
Sort by Popularity
CANCEL APPLY
3. If you view the app logs using adb logcat or the Logcat panel in Android Studio, you will notice the following warnings:

# School of Computer Science

# Page 11 of 13

# COMP5216 Mobile Computing

# LabW04
W/Firestore Adapter: onEvent:error
com.google.firebase.firestore.FirebaseFirestoreException: FAILED_PRECONDITION# COMP5216 lab 4

# CS Tutor | 计算机编程辅导 | Code Help | Programming Help

# WeChat: cstutorcs

# Email: tutorcs@163.com

# QQ: 749389476

# 非中介, 直接联系程序员本人
