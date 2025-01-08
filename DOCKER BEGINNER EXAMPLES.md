Okay, let's explore three practical examples of how you can use the Docker image we created, demonstrating different common scenarios:

**Example 1: Interactive Data Analysis Session**

*   **Goal:** Perform some quick data analysis using pandas in an interactive Python session within the Docker container.
*   **Steps:**
    1.  **Build and run the image** (as described in the previous response).
    2.  Once inside the container's Python prompt, use the `pandas` library:
    ```python
    >>> import pandas as pd
    >>> data = {'col1': [1, 2, 3], 'col2': [4, 5, 6]}
    >>> df = pd.DataFrame(data)
    >>> print(df)
       col1  col2
    0     1     4
    1     2     5
    2     3     6
    >>> print(df.describe())
             col1      col2
    count  3.000000  3.000000
    mean   2.000000  5.000000
    std    1.000000  1.000000
    min    1.000000  4.000000
    25%    1.500000  4.500000
    50%    2.000000  5.000000
    75%    2.500000  5.500000
    max    3.000000  6.000000
    ```

    3.  This shows you can easily create pandas DataFrame and perform analyses.
    4.  Exit the Python prompt by typing `exit()` or pressing `Ctrl+D`.

*   **Explanation:** This demonstrates the container's interactive nature, useful for exploring data, testing out code snippets, and doing exploratory data analysis without having to setup a local environment.

**Example 2: Running a TensorFlow Model (Basic)**

*   **Goal:** Run a very simple TensorFlow model within the Docker container.
*   **Steps:**
    1.  Create a file named `tensorflow_example.py` in the same directory as your Dockerfile and add the following:
        ```python
        import tensorflow as tf
        print("TensorFlow version:", tf.__version__)

        # Simple TensorFlow calculation
        a = tf.constant(2)
        b = tf.constant(3)
        c = tf.add(a, b)
        print("a + b = ", c.numpy())
        ```
    2.  Modify the Dockerfile:
       * Uncomment and change the `COPY` command to copy the script into the image.
       * Uncomment and change the `CMD` command to execute the python script instead of the interactive prompt.
        ```dockerfile
        # Use an official Python 3.10 image as a base
        FROM python:3.10-slim-buster

        # Set the working directory inside the container
        WORKDIR /app

        # Install required Python libraries
        RUN pip install --no-cache-dir pandas tensorflow numpy

        # You can add further commands or configuration here, e.g.,
        COPY tensorflow_example.py .
        CMD ["python", "tensorflow_example.py"]
        ```
    3.  **Rebuild the image:**

         ```bash
        docker build -t python-data-science .
        ```
    4.  **Run the container:**
        ```bash
        docker run python-data-science
        ```

        You should see the output:
        ```
        TensorFlow version: 2.15.0
        a + b =  5
        ```

*   **Explanation:** This example demonstrates running a simple Python script with TensorFlow inside the container. This can easily be scaled to run more complex model trainings, model inference, or any script you have.

**Example 3: Data Processing with Pandas and NumPy**

*   **Goal:** Load some (mock) data, perform some processing using pandas and numpy and save a new result file.
*   **Steps:**
    1. Create a mock `data.csv` in the same directory as your Dockerfile.
      ```csv
      name,age,city
      Alice,30,New York
      Bob,25,London
      Charlie,35,Paris
      David,28,Tokyo
      ```
    2.  Create a `process_data.py` file in the same directory as your Dockerfile with the following contents:
      ```python
      import pandas as pd
      import numpy as np

      # Load the data
      df = pd.read_csv("data.csv")

      # Data Transformation
      df["age_plus_5"] = df["age"] + 5
      df["age_category"] = np.where(df["age"] >= 30, "Old", "Young")

      # Write new csv
      df.to_csv("processed_data.csv", index=False)

      print("Data processing complete. See 'processed_data.csv'")
      ```
   3. Modify the Dockerfile. Add `COPY` command for both `data.csv` and `process_data.py`. Also change the `CMD` to run `process_data.py`
      ```dockerfile
      # Use an official Python 3.10 image as a base
      FROM python:3.10-slim-buster

      # Set the working directory inside the container
      WORKDIR /app

      # Install required Python libraries
      RUN pip install --no-cache-dir pandas tensorflow numpy

      # You can add further commands or configuration here, e.g.,
      COPY data.csv .
      COPY process_data.py .
      CMD ["python", "process_data.py"]
      ```
   4.  **Rebuild the image:**

         ```bash
        docker build -t python-data-science .
        ```
   5.  **Run the container:**
        ```bash
        docker run python-data-science
        ```

        You should see the output:
        ```
        Data processing complete. See 'processed_data.csv'
        ```
   6. Copy the output to the current directory

      ```bash
      docker cp <container_id>:/app/processed_data.csv .
      ```
      You can get the container ID by using `docker ps` after the container finishes running
   7. Open `processed_data.csv` and verify that the csv data is transformed.
     ```csv
      name,age,city,age_plus_5,age_category
      Alice,30,New York,35,Old
      Bob,25,London,30,Young
      Charlie,35,Paris,40,Old
      David,28,Tokyo,33,Young
     ```
*   **Explanation:**
    *   This example demonstrates how you can use pandas and numpy within your Dockerized environment to load and process data. You can add any data source/output to your program.
    *  `docker cp` command can be used to copy data from the container.

**Key takeaways from these examples:**

*   **Reproducibility:** The Docker image provides a consistent environment for your data analysis and machine learning work, ensuring that your code will execute correctly across different machines.
*   **Isolation:** Docker isolates your project's dependencies from your host system, reducing the chances of conflicts.
*   **Portability:**  You can easily share the Docker image with collaborators, allowing them to run your code without any complex setup.
*   **Scalability:** The same docker image can easily be deployed in production environment.

These are just three basic examples, but they highlight the flexibility of using Docker with Python and libraries like pandas, TensorFlow, and numpy for a variety of tasks. Remember to adjust your Dockerfile and Python code based on your needs.
