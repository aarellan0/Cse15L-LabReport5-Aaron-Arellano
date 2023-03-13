# Lab Report 5 - Lab Redo 
# Bash Grading Script
## Aaron Arellano

During lab in week 6, we were assigned the task of creating a bash script that takes in a github repository as a parameter (which needed to include a file
titled ListExamples.java) and then runs JUNIT tests against the student submitted files, assigning a grade to the student depending on the output of the 
tests. This entire script was to be in a file titled *grade.sh*. The final version of my script is seen below.

**grade.sh**

```
CPATH='.:lib/hamcrest-core-1.3.jar:lib/junit-4.13.2.jar'

rm -rf student-submission
git clone $1 student-submission
echo 'Finished cloning'

find student-submission > find_results.txt
FIND=`grep -r "ListExamples.java" find_results.txt`

if [[ -n $FIND ]]
then 
        echo "ListExamples.java file found! Proceeding to compile."
        cp $FIND .
        javac -cp $CPATH *.java
        java -cp $CPATH org.junit.runner.JUnitCore TestListExamples > result.txt
        fail=`grep -c FAILURES!!! result.txt`
        total=`grep -c "@Test" TestListExamples.java`
        if [[ fail -eq 0 ]]
        then
                LASTLINE=`grep "OK " result.txt`
                PASSED=${LASTLINE:4:1}
                cat result.txt
                echo ""
                echo "You Passed all the tests! Congrats!"
                echo "SCORE: $PASSED / $total"
                echo ""
        else
                cat result.txt
                echo ""
                echo "Looks like you failed a few tests. Try again for full credit! :)"
                echo ""
                exit
        fi

else
        echo "ListExamples.java not found. Submission failed."
        exit 
fi
```

The general steps that my script uses is as follows:
1. Removes any existing directory titled student-submission and then uses git clone to clone the new directory
2. Recursviely checks the directory to see if there is a file titled *ListExamples.java* 
3. If no such file exists, the script lets the user knoew and stops.
4. If it does exist, it then checks to see if any JUNIT tests failed.
5. If any tests failed, the script tells the user to debug and try again. 
6. Otherwise, the script counts how many tests passed and returns the grade of 100%

# Bash Script Examples

## Example 1 - list methods lab 3 repository

