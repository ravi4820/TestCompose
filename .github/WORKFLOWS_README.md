# GitHub Actions Configuration

This project uses GitHub Actions for continuous integration and continuous deployment (CI/CD). The following workflows have been configured:

## Workflows Overview

### 1. Android Build CI/CD (`android-build.yml`)
**Triggered on:** Push to `main` or `develop` branches, and on Pull Requests

**Tasks:**
- Sets up JDK 17 and Gradle cache
- Runs full Gradle build
- Executes unit tests
- Builds debug APK
- Builds release APK
- Uploads built APKs as artifacts
- Runs Android Lint checks
- Uploads lint reports

**Runs on:** Ubuntu Latest

### 2. Instrumented Tests (`instrumented-tests.yml`)
**Triggered on:** Push to `main` or `develop` branches, and on Pull Requests

**Tasks:**
- Runs instrumented tests on Android emulator
- Tests against multiple API levels: 28, 31, 34
- Uses macOS environment (better support for emulator)
- Uploads test results as artifacts

**Runs on:** macOS Latest

### 3. Dependency Check (`dependency-check.yml`)
**Triggered on:** Push to `main` or `develop` branches, Pull Requests, and weekly on Sundays

**Tasks:**
- Checks for dependency vulnerabilities
- Verifies all project dependencies
- Uploads dependency reports as artifacts

**Runs on:** Ubuntu Latest

## Project Configuration Details

- **JDK Version:** 17
- **Min SDK:** 24
- **Target SDK:** 36
- **Build System:** Gradle with Kotlin DSL
- **Key Dependencies:** Hilt, Room, Retrofit, OkHttp

## What These Workflows Do

### On Every Push/Pull Request:
1. ✅ Builds the project
2. ✅ Runs unit tests
3. ✅ Runs Android Lint checks
4. ✅ Builds debug APK
5. ✅ Builds release APK
6. ✅ Runs instrumented tests on emulator (API 28, 31, 34)

### Weekly (Sundays):
1. 📦 Checks all dependencies for vulnerabilities
2. 📊 Generates comprehensive dependency reports

## Accessing Build Artifacts

After a successful workflow run:

1. Go to the **Actions** tab in your GitHub repository
2. Select the workflow run
3. Scroll down to find the **Artifacts** section
4. Download APKs or test reports

### Available Artifacts:
- **app-debug** - Debug APK for manual testing
- **app-release** - Release APK ready for distribution
- **lint-results** - Android Lint analysis reports
- **instrumented-test-results** - Test results from emulator tests
- **dependency-reports** - Vulnerability and dependency analysis

## Setup Instructions

No additional setup is required! The workflows are automatically triggered when:
- You push commits to `main` or `develop` branches
- You create a pull request to these branches

## Local Development

To build locally:

```bash
# Build the project
./gradlew build

# Run unit tests
./gradlew test

# Run Android Lint
./gradlew lint

# Build debug APK
./gradlew assembleDebug

# Build release APK
./gradlew assembleRelease

# Run instrumented tests (requires emulator)
./gradlew connectedAndroidTest
```

## Customization

### Modifying Triggers
Edit the workflow files to change when they run. For example, to trigger on all branches:

```yaml
on:
  push:
    branches: [ '**' ]
```

### Changing API Levels for Testing
In `instrumented-tests.yml`, modify the `api-level` matrix:

```yaml
matrix:
  api-level: [26, 28, 30, 31, 33, 34]
```

### Adding Slack/Email Notifications
You can add notifications to workflows. See [GitHub Actions documentation](https://docs.github.com/en/actions).

## Troubleshooting

### Build Failures
1. Check the **Actions** tab for error logs
2. Common issues:
   - JDK version mismatch
   - Missing credentials or signing keys for release builds
   - Dependency version conflicts

### Test Failures
1. Review the uploaded test result artifacts
2. Run tests locally to reproduce
3. Check emulator API level compatibility

### Dependency Check Warnings
1. Review the vulnerable dependencies in the report
2. Update to patched versions in `gradle/libs.versions.toml`
3. Run `./gradlew dependencyUpdate` to check for updates

## Next Steps

1. **Set up release signing** (if building release APKs):
   - Store your keystore file securely in GitHub Secrets
   - Configure signing in `build.gradle.kts`

2. **Add code coverage** reporting:
   - Integrate JaCoCo or similar tools
   - Upload to code coverage services (Codecov, etc.)

3. **Add distribution** (upload to Google Play, Firebase):
   - Configure GitHub Secrets with credentials
   - Add deployment steps to workflows

4. **Add status badges** to your README:
   ```markdown
   ![Build Status](https://github.com/YOUR_USERNAME/JetpackCompose/workflows/Android%20Build%20CI%2FCD/badge.svg)
   ```

## References

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Android Gradle Plugin Guide](https://developer.android.com/build)
- [Gradle Project Configuration](https://gradle.org/guides/creating-new-gradle-builds/)

