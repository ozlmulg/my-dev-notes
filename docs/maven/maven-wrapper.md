# Maven Wrapper

The **Maven Wrapper** has officially been released by the Apache Maven Project!

---

## What is Maven Wrapper?

Maven Wrapper allows you to automatically download and use a specific version of Maven for your project without
requiring users to install Maven manually on their machines.

---

## Resources

- **GitHub Repository:** [apache/maven-wrapper](https://github.com/apache/maven-wrapper)
- **Official Site:** [maven.apache.org/wrapper](https://maven.apache.org/wrapper)
- **JIRA Issues:** [MWRAPPER project issues](https://issues.apache.org/jira/projects/MWRAPPER/issues/MWRAPPER)

---

## How to Use

Run the following command in the **root directory** of your Maven project to add the Maven Wrapper scripts:

```bash
mvn wrapper:wrapper
```

## Using a Specific Maven Version

To generate wrapper files for a specific Maven version (e.g., Maven 3.9.5), run:

```bash
mvn wrapper:wrapper -Dmaven=3.9.5
```

This will ensure everyone working on the project uses the specified Maven version.

---

## Benefits

- No need to install Maven globally
- Consistent Maven version across all environments
- Simplifies build setup for new developers

---

For more details, check the [official Maven Wrapper documentation](https://maven.apache.org/wrapper).
