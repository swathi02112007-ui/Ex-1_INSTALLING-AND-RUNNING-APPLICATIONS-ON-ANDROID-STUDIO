# Ex-1_INSTALLING-AND-RUNNING-APPLICATIONS-ON-ANDROID-STUDIO

## AIM:
To develop, install, and execute a simple QuizMaster Android application using Android Studio. The application allows users to answer multiple-choice questions, check their scores, and play the quiz again.

## PROCEDURE:

1. Open Android Studio and create a new project.
2. Select the Empty Views Activity template.
3. Enter the application name as QuizMaster.
4. Select Java as the programming language.
5. Choose the minimum SDK version and click Finish.
6. Design the welcome screen using XML layouts.
7. Add a brain icon, application title, welcome message, and Start Quiz button.
8. Write Java code to handle the Start Quiz button click event.
9. Create quiz questions with multiple-choice answers.
10. Implement the logic to check answers and calculate the score.
11. Design the quiz completion screen to display the final score and Play Again button.
12. Connect the Android Emulator to Android Studio.
13. Build and run the application on the emulator.
14. Test the application by answering all five quiz questions.
15. Verify that the application displays the final score correctly.

## PROGRAM:

# AndroidManifest.xml

```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <application
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.MyFirstApp">


        <activity
            android:name=".ResultActivity"
            android:exported="false" />

        <activity
            android:name=".QuizActivity"
            android:exported="false" />

        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>


    </application>

</manifest>
```

# MainActivity.java

```
package com.example.myfirstapp;

import android.content.Intent;
import android.os.Bundle;
import android.widget.Button;
import androidx.appcompat.app.AppCompatActivity;

public class MainActivity extends AppCompatActivity {

    Button startQuizButton;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        startQuizButton = findViewById(R.id.startQuizButton);

        startQuizButton.setOnClickListener(v -> {
            Intent intent = new Intent(MainActivity.this, QuizActivity.class);
            startActivity(intent);
        });
    }
}

```

# QuizActivity.java

```
package com.example.myfirstapp;

import android.content.Intent;
import android.os.Bundle;
import android.widget.Button;
import android.widget.RadioButton;
import android.widget.RadioGroup;
import android.widget.TextView;
import android.widget.Toast;

import androidx.appcompat.app.AppCompatActivity;

import java.util.ArrayList;
import java.util.Collections;

public class QuizActivity extends AppCompatActivity {

    TextView questionNumber, questionText;
    RadioGroup optionsGroup;
    RadioButton option1, option2, option3, option4;
    Button backButton, nextButton;

    String[] questions = {
            "Which language is used for Android app development?",
            "Which company developed Java?",
            "What does CPU stand for?",
            "Which symbol ends a Java statement?",
            "Which is an input device?",
            "Which keyword creates a class in Java?",
            "Which language is mainly used for web page structure?",
            "What does SQL stand for?",
            "Which data type stores true or false?",
            "Which loop executes at least once?",
            "Which command displays output in Java?",
            "Which database command retrieves data?",
            "Which symbol starts a comment in Python?",
            "Which keyword defines a function in Python?",
            "Which device displays computer output?",
            "Which SQL command removes a table?",
            "Which keyword creates an object in Java?",
            "Which data type stores whole numbers in Java?",
            "What does RAM stand for?",
            "Which language is commonly used for AI?"
    };

    String[][] options = {
            {"HTML", "Java", "CSS", "SQL"},
            {"Microsoft", "Apple", "Sun Microsystems", "Amazon"},
            {"Central Processing Unit", "Computer Personal Unit",
                    "Central Program Utility", "Control Power Unit"},
            {".", ",", ";", ":"},
            {"Monitor", "Keyboard", "Speaker", "Printer"},
            {"method", "class", "define", "create"},
            {"HTML", "SQL", "Java", "C"},
            {"Structured Query Language", "Simple Question Language",
                    "System Query Link", "Standard Quick Language"},
            {"int", "char", "boolean", "String"},
            {"for", "while", "do-while", "if"},
            {"System.out.println()", "printLine()", "display()", "show()"},
            {"UPDATE", "SELECT", "DELETE", "CREATE"},
            {"//", "#", "/*", "$"},
            {"function", "fun", "def", "method"},
            {"Keyboard", "Mouse", "Monitor", "Microphone"},
            {"REMOVE", "DELETE DATABASE", "DROP TABLE", "CLEAR"},
            {"new", "this", "static", "void"},
            {"boolean", "int", "char", "String"},
            {"Read Access Memory", "Random Access Memory",
                    "Run Access Module", "Read Active Machine"},
            {"Python", "HTML", "CSS", "XML"}
    };

    int[] answers = {
            1, 2, 0, 2, 1,
            1, 0, 0, 2, 2,
            0, 1, 1, 2, 2,
            2, 0, 1, 1, 0
    };

    ArrayList<Integer> questionList = new ArrayList<>();
    int[] selectedAnswers = new int[5];
    int currentQuestion = 0;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_quiz);

        questionNumber = findViewById(R.id.questionNumber);
        questionText = findViewById(R.id.questionText);
        optionsGroup = findViewById(R.id.optionsGroup);

        option1 = findViewById(R.id.option1);
        option2 = findViewById(R.id.option2);
        option3 = findViewById(R.id.option3);
        option4 = findViewById(R.id.option4);

        backButton = findViewById(R.id.backButton);
        nextButton = findViewById(R.id.nextButton);

        for (int i = 0; i < questions.length; i++) {
            questionList.add(i);
        }

        Collections.shuffle(questionList);

        for (int i = 0; i < 5; i++) {
            selectedAnswers[i] = -1;
        }

        showQuestion();

        backButton.setOnClickListener(v -> {
            saveAnswer();

            if (currentQuestion > 0) {
                currentQuestion--;
                showQuestion();
            }
        });

        nextButton.setOnClickListener(v -> {
            int selectedId = optionsGroup.getCheckedRadioButtonId();

            if (selectedId == -1) {
                Toast.makeText(this, "Please select an answer",
                        Toast.LENGTH_SHORT).show();
                return;
            }

            saveAnswer();

            if (currentQuestion < 4) {
                currentQuestion++;
                showQuestion();
            } else {
                calculateScore();
            }
        });
    }

    void showQuestion() {
        int q = questionList.get(currentQuestion);

        questionNumber.setText("Question " + (currentQuestion + 1) + " of 5");
        questionText.setText(questions[q]);

        option1.setText(options[q][0]);
        option2.setText(options[q][1]);
        option3.setText(options[q][2]);
        option4.setText(options[q][3]);

        optionsGroup.clearCheck();

        if (selectedAnswers[currentQuestion] != -1) {
            int answer = selectedAnswers[currentQuestion];

            if (answer == 0) {
                option1.setChecked(true);
            } else if (answer == 1) {
                option2.setChecked(true);
            } else if (answer == 2) {
                option3.setChecked(true);
            } else {
                option4.setChecked(true);
            }
        }

        backButton.setEnabled(currentQuestion > 0);

        if (currentQuestion == 4) {
            nextButton.setText("Submit Quiz");
        } else {
            nextButton.setText("Next");
        }
    }

    void saveAnswer() {
        int selectedId = optionsGroup.getCheckedRadioButtonId();

        if (selectedId == R.id.option1) {
            selectedAnswers[currentQuestion] = 0;
        } else if (selectedId == R.id.option2) {
            selectedAnswers[currentQuestion] = 1;
        } else if (selectedId == R.id.option3) {
            selectedAnswers[currentQuestion] = 2;
        } else if (selectedId == R.id.option4) {
            selectedAnswers[currentQuestion] = 3;
        }
    }

    void calculateScore() {
        int score = 0;

        for (int i = 0; i < 5; i++) {
            int q = questionList.get(i);

            if (selectedAnswers[i] == answers[q]) {
                score++;
            }
        }

        Intent intent = new Intent(QuizActivity.this, ResultActivity.class);
        intent.putExtra("score", score);
        intent.putExtra("total", 5);
        startActivity(intent);
        finish();
    }
}

```

# ResultActivity.java

```
package com.example.myfirstapp;

import android.content.Intent;
import android.os.Bundle;
import android.widget.Button;
import android.widget.TextView;
import androidx.appcompat.app.AppCompatActivity;

public class ResultActivity extends AppCompatActivity {

    TextView scoreText, feedbackText;
    Button playAgainButton;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_result);

        scoreText = findViewById(R.id.scoreText);
        feedbackText = findViewById(R.id.feedbackText);
        playAgainButton = findViewById(R.id.playAgainButton);

        int score = getIntent().getIntExtra("score", 0);
        int total = getIntent().getIntExtra("total", 5);

        scoreText.setText("Your Score: " + score + "/" + total);

        if (score == total) {
            feedbackText.setText("Excellent! You got every answer correct.");
        } else if (score >= total / 2) {
            feedbackText.setText("Good job! Keep practising.");
        } else {
            feedbackText.setText("Keep learning. Try again!");
        }

        playAgainButton.setOnClickListener(v -> {
            Intent intent = new Intent(ResultActivity.this, MainActivity.class);
            intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP |
                    Intent.FLAG_ACTIVITY_NEW_TASK);
            startActivity(intent);
            finish();
        });
    }
}
```

# activity_main.xml

```
<?xml version="1.0" encoding="utf-8"?>
<ScrollView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#F4F1FF">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:gravity="center"
        android:orientation="vertical"
        android:padding="28dp">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="🧠"
            android:textSize="72sp" />

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="16dp"
            android:text="QuizMaster"
            android:textColor="#512DA8"
            android:textSize="36sp"
            android:textStyle="bold" />

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="12dp"
            android:gravity="center"
            android:text="Challenge Your Knowledge!"
            android:textColor="#333333"
            android:textSize="20sp" />

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="12dp"
            android:gravity="center"
            android:text="Answer simple questions and check your score."
            android:textColor="#666666"
            android:textSize="16sp" />

        <Button
            android:id="@+id/startQuizButton"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginTop="36dp"
            android:backgroundTint="#673AB7"
            android:padding="12dp"
            android:text="Start Quiz"
            android:textColor="#FFFFFF"
            android:textSize="18sp" />

    </LinearLayout>
</ScrollView>
```

# activity_quiz.xml

```
<?xml version="1.0" encoding="utf-8"?>
<ScrollView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#F4F1FF">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:padding="24dp">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="QuizMaster"
            android:textColor="#512DA8"
            android:textSize="28sp"
            android:textStyle="bold" />

        <TextView
            android:id="@+id/questionNumber"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="24dp"
            android:text="Question 1 of 5"
            android:textColor="#666666"
            android:textSize="16sp" />

        <TextView
            android:id="@+id/questionText"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginTop="20dp"
            android:text="Question appears here"
            android:textColor="#222222"
            android:textSize="23sp"
            android:textStyle="bold" />

        <RadioGroup
            android:id="@+id/optionsGroup"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginTop="24dp"
            android:orientation="vertical">

            <RadioButton
                android:id="@+id/option1"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:padding="12dp"
                android:text="Option 1"
                android:textSize="17sp" />

            <RadioButton
                android:id="@+id/option2"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:padding="12dp"
                android:text="Option 2"
                android:textSize="17sp" />

            <RadioButton
                android:id="@+id/option3"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:padding="12dp"
                android:text="Option 3"
                android:textSize="17sp" />

            <RadioButton
                android:id="@+id/option4"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:padding="12dp"
                android:text="Option 4"
                android:textSize="17sp" />

        </RadioGroup>

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginTop="28dp"
            android:orientation="horizontal">

            <Button
                android:id="@+id/backButton"
                android:layout_width="0dp"
                android:layout_height="wrap_content"
                android:layout_weight="1"
                android:layout_marginEnd="8dp"
                android:backgroundTint="#9E9E9E"
                android:text="Back"
                android:textColor="#FFFFFF" />

            <Button
                android:id="@+id/nextButton"
                android:layout_width="0dp"
                android:layout_height="wrap_content"
                android:layout_weight="1"
                android:layout_marginStart="8dp"
                android:backgroundTint="#673AB7"
                android:text="Next"
                android:textColor="#FFFFFF" />

        </LinearLayout>

    </LinearLayout>
</ScrollView>
```

# activity_result.xml

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="vertical"
    android:padding="28dp"
    android:background="#F4F1FF">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="🎉"
        android:textSize="72sp" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="16dp"
        android:text="Quiz Completed!"
        android:textColor="#512DA8"
        android:textSize="30sp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/scoreText"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="24dp"
        android:text="Your Score: 0/5"
        android:textColor="#222222"
        android:textSize="24sp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/feedbackText"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="12dp"
        android:gravity="center"
        android:text="Good job!"
        android:textColor="#666666"
        android:textSize="18sp" />

    <Button
        android:id="@+id/playAgainButton"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="32dp"
        android:backgroundTint="#673AB7"
        android:padding="12dp"
        android:text="Play Again"
        android:textColor="#FFFFFF"
        android:textSize="18sp" />

</LinearLayout>
```

## OUTPUT:

# QuizMaster Welcome Screen:

<img width="1917" height="1017" alt="Screenshot 2026-11-05 103124" src="https://github.com/user-attachments/assets/07af9e43-9c65-4300-b023-2ed36d961c5f" />

# Quiz Questions Screen:

<img width="1917" height="1022" alt="Screenshot 2026-11-05 103510" src="https://github.com/user-attachments/assets/15354b22-97c4-4090-9d5a-63eb3e8322a3" />

# Quiz Completed Screen:

<img width="1917" height="1022" alt="Screenshot 2026-11-05 103113" src="https://github.com/user-attachments/assets/f1691c85-c122-40a6-b042-1147e81313d8" />


## RESULT:

Thus, the QuizMaster Android application was successfully developed, installed, and executed using Android Studio. The application displayed the welcome screen and quiz completion screen successfully. The quiz score was calculated and displayed correctly as 5/5.
