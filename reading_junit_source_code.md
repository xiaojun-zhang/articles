#Reading JUnit Source Code
Robin Zhang

JUnit is a widely used unit testing tool in Java projects, and the source code is extremely simple and elegant. It's a very good choice for Java programers who want to digging into source code and open source projects. The JUnit version discussed in this article is 3.8.2 .

###Preparation and JUnit Basic Usage
First step is to download the JUnit source code, create a new project in Eclipse, and then import the downloaded JUnit code.

Second step is to define the code to be tested. Below is the code, it's very simple, just calculates the sum of two integers.

    public class SimpleClass {
        public static int add(int a, int b) {
            return a + b;
        }
    }

Third step is to write the test case. JUnit test cases can be implemented by inheriting the junit.framework.TestCase class. Methods, whose name begins with 'test', defined in subclasses of 'TestCase' will be treated as test cases. Multiple test cases can form a TestSuite. JUnit4 can also define test cases with @Test annotation.

    public class TestSimpleClass extends TestCase {
        public void testAdd() {
            int result = SimpleClass.add(1, 1);
            Assert.assertEquals(2, result);
        }
    }

In the above code, the testAdd() method is parsed as a test case. You can also define a test case by defining the suite() method, which isn't too much difference.

Now we are ready to run and test the code. 

###JUnit Source Code Structure

Below is JUnit's package structure:

![Package Stucture](1.png =300x)

The core code is in junit.framework package. Package junit.runner is for running the test cases, in which BaseTestRunner is an abstract class. Package Junit.awtui, junit.swingui, junit.textui are three different graphical interfaces to run test cases, each package has a TestRunner inherited from junit.runner.BaseTestRunner.

The code I red is mainly in junit.framework package, junit.textui package, and the abstract class BaseTestRunner.

Below is the class diagram:

![Class Diagram](2.png =500x)

###Running Process of JUnit

JUnit's running process can be divided into three steps, preparaing test cases, running test cases and collecting test results. In following code, getTest(testcase) is to prepare test cases, doRun(suite, wait) is to run the test cases:

    try {
        if (!method.equlas(""))
            return runSingleMethod(testCase, method, wait);
            
        Test suite = getTest(testCase);
        return doRun(suite, wait);
    } catch (Exception e) {
        throw new Exception("Could not create and run test suite: " + e);
    }


Below is the sequence diagram:

![Sequence Diagram](3.png =500x)

###Design Pattern in JUnit####CompositeJUnit uses composite pattern when creating test cases. Junit.framework.Test is the interface that defines the test case, which includes both run() and countTestCases () methods. TestCase and TestSuite are two classes that implement the interface, TestCase is for single test case, and TestSuite is for a  collection of test cases.####Observer
Observer pattern is used when collecting test results. junit.framework.TestResult manges a collection of TestListeners, these TestLiteners will be notified when execution of test case is failed.
Manage a collection of TestListener:

    /**
    * Registers a TestListener
    */
    public synchronized void addListener(TestListenser listener) {
        fListeners.addElement(listener);
    }
    
    /**
    *  unregisters a TestListener
    */
    public synchronized void removeListener(TestListenser listener) {
        fListeners.removeElement(listener);
    }When the test case fails to execute, notify TestListenner to handle the errors:

    public synchronized void addError(Test test, Throwable t) {
        fErrors.addElement(new TestFailure(test, t));
        for (Enumeration e = cloneListeners().elements; e.hasMoreElements; ) {
            ((TestListener)e.nextElement()).addError(test, t);
        }
    }####Template Method
Template mode is relatively simple, it's used in BaseTestRunner.getTest(), TestCase.runBare().
