Download Link: https://assignmentchef.com/product/solved-icsi410-program-1
<br>
In this programming assignment, you need to implement several classes for representing both relational and non-relational data. You first need to install and run Eclipse on your machine and import the “hdb data” project (see Appendix A). Please generate an API document (see Appendix B) and then take a look at that document as well as the source code to familiarize yourself with this assignment. This assignment provides you with a set of incomplete classes. To see these classes, choose the “src” source folder and then either the “hdb.data.relational” package or the “hdb.data.nonrelational” package. You will need to write code for these classes. Your code will be graded by running a set of unit tests (see the unit tests in the “test” source folder which use JUnit<a href="#_ftn1" name="_ftnref1"><sup>[1]</sup></a>) and then examining your code. For details of running these tests, refer to Appendix A. Note that passing unit tests does <em>not </em>necessarily guarantee that your implementation is correct and efficient. Please make sure that your code would <em>not </em>cause problems even in situations not covered by the unit tests. If you have questions, please contact the TA(s) or the instructor. The remainder of this document describes the components that you need to implement.

<h1>Part 1. Relation Schema</h1>

A RelationSchema represents the schema of a relation. A RelationSchema contains an array attributeNames which stores the names of attributes (e.g., { “ID”, “Name” }) and another array attributeTypes which contains the types of the attributes (e.g., { Integer.class, String.class }). In this part, you need to complete the following methods of the RelationSchema class:

<ul>

 <li>size(): returns the number of attributes in the RelationSchema.</li>

 <li>attributeName(int attributeIndex): returns the name of the attribute specified by attributeIndex. For example, if array attributeNames is set to { “ID”, “Name” }, then attributeName(0) and attributeName(1) must return “ID” and “Name”, respectively.</li>

</ul>

attributeType(int attributeIndex): returns the type of the attribute specified by attributeIndex. For example, if array attributeTypes is set to { Integer.class, String.class }, then attributeType(0) and attributeType(1) must return Integer.class and String.class, respectively.

<ul>

 <li>attributeIndex(String attributeName): returns the index of the attribute specified by attributeName. For example, if array attributeNames is set to { “ID”, “Name” }, then attributeIndex(“ID”), attributeIndex(“Name”), and attributeIndex(“Address”) must return 0, 1, and null, respectively.</li>

 <li>save(String fileName): writes the RelationSchema (on which this method is called) to the file specified by fileName. This method needs to create a FileOutputStream using fileName and then an ObjectOutputStream that writes to that FileOutputStream. The RelationSchema on which this method is called needs to be written to the ObjectOutputStream using ObjectOutputStream#writeObject(Object). If your code throws a io.NotSerializableException, address that problem after understanding the basics of Java object serialization. You would need to make a change outside the save(String fileName) method. Details of serialization can be found at: https://docs.oracle.com/ javase/8/docs/technotes/guides/serialization/index.html.</li>

</ul>

Please make sure that your code passes all of the 5 unit tests in RelationSchemaTest. These unit tests verify the 5 methods mentioned above using one instance of RelationSchema containing 2 attributes (“ID” and “Temperature” of the Integer and Double types, respectively) and another instance containing 3 attributes (“ID”, “Name”, and “Address” of the Integer, String, and String types, respectively) .

<h1>Part 2. Tuple</h1>

A Tuple is a record (i.e., a row) in a relation. Each Tuple contains the values of certain attributes that collectively represent an entity. For example, a tuple { 123, “John” } in a relation with attributes “ID” and “Name” would represent a person whose ID is 123 and whose name is “John”. Each Tuple has an array attributeValues which stores the values of attributes defined in the corresponding RelationSchema.

In this part, you need to implement the following methods in Tuple.java:

<ul>

 <li>setAttribute(int attributeIndex, Object o): sets the value of the attribute specified by attributeIndex to Objecto. For example, setAttribute(0, 123) and setAttribute(1, “John”) would set attributeValues[0] to 123, and attributeValues[1] to “John”, respectively. If the specified attribute value (o) is not compatible with the type of the attribute, then the method needs to throw a TypeException. In other words, when the RelationSchema has an array of types { class, String.class }, setAttribute(0, “John”) must throw a TypeException since the attribute at index 0 is of the Integer type and thus cannot be set to “John”. Consider using Class#isInstance(Object) to check type compatibility.</li>

 <li>writeAttributes(ObjectOutputStream out): writes the attribute values of the Tuple to the specified ObjectOutputStream. This method needs to call Tuple#write(Object o, ObjectOutputStream out) to write each attribute value to the ObjectOutputStream. As explained below, Java object serialization tends to incur high space overhead. The purpose of writeAttributes(ObjectOutputStream out) is to reduce this overhead through custom serialization. Right after implementing this method, consider verifying your implementation using the writeAttributes test in TupleTest as explained below this list of methods to implement.</li>

</ul>

write(Object o, ObjectOutputStream out): writes the specified Object to the specified ObjectOutputStream. Writing Object o to the ObjectOutputStream using

ObjectOutputStream#writeObject(Object) (which performs the standard Java object serialization) may incur high space overhead. Therefore, write(Object o, ObjectOutputStream out) needs to call (i) ObjectOutputStream#writeInt(int) if the given Object is of the Integer type, (ii) ObjectOutputStream#writeDouble(double) if the given Object is of the Double type, and (iii) ObjectOutputStream#writeObject(Object) only in the other cases.

<ul>

 <li>read(Class&lt;?&gt; type, ObjectInputStream in): reads an object from the specified ObjectInputStream. This method needs to call (i) ObjectInputStream#readInt() if the specified type is class, (ii) ObjectInputStream#readDouble() if the specified type is Double.class, and (iii) ObjectInputStream#readObject() otherwise.</li>

</ul>

Please verify your code using the tests in TupleTest. Your code can pass the writeAttributes test in TupleTest as long as writeAttributes(ObjectOutputStream out) is correctly implemented (even if write(Object o, ObjectOutputStream out) and read(Class&lt;?&gt; type, ObjectInputStream in) are not completed). The following result from the writeAttributes test shows the difference between the standard Java object serialization and our custom serialization (even when it is partially implemented) given a Tuple contaning just one 4-byte int value and one 8-byte double value.

size of tuple [1 ,      5.0]         (standard Java serialization ): 538 size of tuple [1 ,              5.0]                (custom serialization ):          134

The writeRead test in TupleTest requires complete implementation of write(Object o, ObjectOutputStream out) and read(Class&lt;?&gt; type, ObjectInputStream in). When Part 2 is completed, the writeRead test will produce the following result:

size of tuple [1 ,      5.0]         (standard Java serialization ): 538 size of tuple [1 ,              5.0]                (custom serialization ):          18

The above result shows 6 bytes of additional data written in addition to the 4-byte int and 8byte double values due to an inherent overhead caused by the use of ObjectOutputStream. This additional space overhead is still much lower than that of the standard Java object serialization. In other words, the change in space usage from 538 bytes to 18 bytes shows the benefit of custom serialization over the standard Java object serialization.

<h1>Part 3. Non-Relational Data</h1>

In this part, you need to complete the CollectionSchema and DataObject classes in the hdb.data.nonrelational package. These two classes are similar to RelationSchema and Tuple, but their main difference is that they support non-relational data. In detail, while all Tuples in the same relation have the same set of attributes, DataObjects in the same collection can have different attributes. Furthermore, a DataObject may have a hierarchical structure. For example, for a DataObject, the value of the “Name” attribute can be another DataObject having “FirstName” and “LastName” as its attributes. In this case, given the former DataObject, “Name.FirstName” and “Name.LastName” would represent the first name and second name of a person, respectively.

The CollectionSchemaTestBasic and DataObjectTestBasic classes in the test source folder are designed to verify the correctness of the code when the CollectionSchema and DataObject classes do not fully support hierarchical data representation. The CollectionSchemaTestAdvanced and DataObjectTestAdvanced classes check whether or not CollectionSchema and DataObject can represent data in a hierarchical manner.

Each CollectionSchema uses three HashMaps to maintain a hierarchy of attributes:

name2index: maps attribute names to attribute indices. For example, when “ID” and “Name” are assigned indices 0 and 1, name2index will look like {“ID”=0, “Name”=1}.

<ul>

 <li>index2name: maps attribute indices to attribute names. For example, when “ID” and “Name” are assigned indices 0 and 1, index2name will look like {0=”ID”, 1=”Name”}.</li>

 <li>index2schema: maps an attribute index to a CollectionSchema if the corresponding attribute consists of sub-attributes. For example, if “ID” has no sub-attributes and “Name” has two sub-attributes FirstName and SecondName, then index2schema will look like {1={0=”FirstName”, 1=”LastName”}}. In this example, {0=”FirstName”, 1=”LastName”}) represents a CollectionSchema consisting of attributes “FirstName” and “LastName”. Also note that index2schema in the above example does not have any entry for “ID” because it does not have any sub-attributes. In CollectionSchema, you need to complete the following methods:</li>

 <li>int[] attributeIndex(String attributeName): returns the index (as an int array) of the attribute specified by attributeName. For example, suppose that (i) “ID” and “Name” are assigned indices 0 and 1, respectively, and (ii) “Name” has sub-attributes “FirstName” and “LasstName” which are assigned 0 and 1, respectively. Then, attributeIndex(“ID”) and attributeIndex(“Name”) need to return arrays {0} and {<a href="#_ftn2" name="_ftnref2"><sup>[2]</sup></a>}, respecitvely. Furthermore, attributeIndex(“Name.FirstName”) and attributeIndex(“Name.LastName”) need to return arrays {1, 0} and {1, 1}, respecitvely. If the attribute specified by attributeName is not yet regisered in name2index, then this attributeIndex(String attributeName) method needs to register that attribute (essentially, the name and index of that attribute) in name2index. In this case, attributeIndex(String attributeName) needs to update index2name and may have to update index2schema if necessary.</li>

 <li>attributeName(int[] attributeIndex): returns the name of the attribute specified by attributeIndex. In the example mentioned above, attributeName({0}) and attributeName({1}) need to return “ID” and “Name”, respecitvely. Furthermore, attributeName({1, 0}) and attributeName({1, 1}) need to return “Name.FirstName” and “Name.LastName”, respecitvely.</li>

</ul>

In DataObject.java, you need to implement the following:

<ul>

 <li>setAttribute(String attributeName, Object o): sets the attribute specified by attributeName to o.</li>

 <li>attributeValue(int[] attributeIndex): returns the value of the attribute specified by attributeIndex.</li>

 <li>writeAttributes(ObjectOutputStream out): writes the attributes of the DataObject on which the method is called to the specified ObjectOutputStream.</li>

 <li>DataObject(CollectionSchema schema, ObjectInputStream in): constructs a DataObject from the specified ObjectInputStream. This constructor needs to throw an</li>

</ul>

InvalidAttributeIndexException if an attribute index stored in the ObjectInputStream is not registered in the CollectionSchema for this DataObject (i.e., an invalid attribute index).

<h1>Appendix A. Installing Eclipse and Importing a Java Project</h1>




Figure 1: Eclipse

<ol start="2">

 <li>From the web site, download the eclipse installer (those for Linux, Windows, and Mac OS X are available) and then choose “Eclipse IDE” and install it.</li>

 <li>After finishing installation, start Eclipse.</li>

 <li>When Eclipse runs for the first time, it asks the user to choose the workspace location. You may use the default location.</li>

 <li>In the menu bar, choose “File” and then “Import”. Next, select “General” and “Existing Projects into Workspace”. Then, click the “Browse” button and select the “hdb zip” file contained in this assignment package.</li>

 <li>Once the project is imported, you can choose the “data” package in the “test” source folder. Then, you can run one among RelationSchemaTest.java, TupleTest.java,</li>

</ol>

CollectionSchemaTestBasic.java, CollectionSchemaTestAdvanced.java,

DataObjectTestBasic.java, and DataObjectTestAdvanced.java by clicking the icon highlighted in Figure 1.

<h1>Appendix B. Creating API documents using javadoc</h1>

One nice feature of Java is its support for “documentation comments”, or “javadoc” comments, which you can use to automatically produce documentation for your code. Javadoc comments start with “/**”. Inside a javadoc comment, there are some special symbols, like @param and @return.

You can create HTML-based API documents from the source as follows:

<ol>

 <li>Click the “hdb data” project icon in the Navigator or Project Explorer window.</li>

 <li>Select “Generate Javadoc” from the “Project” menu.</li>

 <li>In the “Generate Javadoc” dialog box, press the “Finish” button.</li>

</ol>

As it runs, it tells you that it’s generating various things. When it is finished, a few new folders should appear in your project: doc, doc.resources, and so on. See what got generated (to open the newly created HTML documentation files in a web browser window, just double-click them; you can start with “index.html”).

<a href="#_ftnref1" name="_ftn1">[1]</a> http://junit.org

<a href="#_ftnref2" name="_ftn2">[2]</a> . Visit:

http://www.eclipse.org/downloads/