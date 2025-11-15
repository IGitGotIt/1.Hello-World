# CLAUDE.md - AI Assistant Guide for Hello-World iOS Project

## Project Overview

**Project Name:** Hello-World
**Type:** iOS Application (Objective-C)
**Purpose:** OpenTok (Vonage Video API) integration demo with JWT-based signal encryption
**Copyright:** TokBox, Inc. (2013)
**iOS Target:** 6.0+

This is a demonstration application that showcases real-time audio/video communication using the OpenTok SDK, combined with JWT (JSON Web Token) encryption for secure signal transmission between participants in a session.

### What This App Does
1. Connects to an OpenTok session using API credentials
2. Publishes audio/video stream from the device
3. Subscribes to other participants' streams
4. Sends encrypted signals using JWT encoding with a shared secret
5. Receives and decrypts signals in real-time
6. Displays the decrypted signal text in the UI

## Repository Structure

```
1.Hello-World/
├── Hello-World/                   # Main source directory
│   ├── ViewController.h/m         # Main UI controller (OpenTok integration)
│   ├── AppDelegate.h/m           # Application lifecycle management
│   ├── main.m                    # Application entry point
│   ├── JWT.h/m                   # JWT encoding/decoding core
│   ├── JWTAlgorithm*.h/m         # JWT algorithm implementations (HS256, HS512, None)
│   ├── JWTClaimsSet.h/m          # JWT claims set handling
│   ├── JWTClaimsSetSerializer.h/m # JWT serialization
│   ├── JWTAlgorithmFactory.h/m   # Algorithm factory pattern
│   ├── NSString+JWT.h/m          # JWT category extensions for NSString
│   ├── NSData+JWT.h/m            # JWT category extensions for NSData
│   ├── MF_Base64Additions.h/m   # Base64 encoding/decoding utilities
│   ├── Base.lproj/               # Localized resources
│   │   ├── Main_iPhone.storyboard # iPhone interface
│   │   └── Main_iPad.storyboard   # iPad interface
│   ├── Images.xcassets/          # Image assets
│   │   ├── AppIcon.appiconset    # App icons
│   │   └── LaunchImage.launchimage # Launch screens
│   ├── en.lproj/                 # English localization
│   └── Hello-World-Info.plist    # App configuration
├── Hello-WorldTests/             # Unit tests directory
│   ├── Hello_WorldTests.m        # Test cases
│   └── Hello-WorldTests-Info.plist
├── Hello-World.xcodeproj/        # Xcode project configuration
├── Hello-World.xcworkspace/      # Xcode workspace (CocoaPods)
├── Podfile                       # CocoaPods dependencies
├── Podfile.lock                  # Locked dependency versions
├── gitPics/                      # Screenshots for documentation
└── README.md                     # Project documentation

```

## Key Technologies & Dependencies

### Dependencies (via CocoaPods)
- **OpenTok SDK**: Real-time audio/video communication platform
  - Platform: iOS 6.0+
  - Defined in: `Podfile`

### Custom Implementations
- **JWT Library**: Complete JWT implementation supporting:
  - HS256 (HMAC with SHA-256)
  - HS512 (HMAC with SHA-512)
  - None (unsigned tokens)
  - Base64 URL-safe encoding/decoding
  - Claims set serialization

## Architecture & Design Patterns

### Model-View-Controller (MVC)
- **View**: Storyboards (`Main_iPhone.storyboard`, `Main_iPad.storyboard`)
- **Controller**: `ViewController` (manages OpenTok session and UI)
- **Model**: JWT classes, OpenTok data models

### Delegate Pattern
The `ViewController` implements multiple OpenTok delegate protocols:
- `OTSessionDelegate`: Session lifecycle events
- `OTSubscriberKitDelegate`: Subscriber events
- `OTPublisherDelegate`: Publisher events

### Factory Pattern
- `JWTAlgorithmFactory`: Creates appropriate JWT algorithm instances

### Category Extensions
- `NSString+JWT`: String utilities for JWT operations
- `NSData+JWT`: Data utilities for JWT operations

## Core Components

### 1. ViewController (ViewController.h/m)

**Location:** `Hello-World/ViewController.m:1`

**Responsibilities:**
- OpenTok session management
- Publishing local audio/video streams
- Subscribing to remote streams
- Sending/receiving encrypted signals
- UI updates

**Key Properties:**
```objc
OTSession* _session;      // OpenTok session instance
OTPublisher* _publisher;  // Local stream publisher
OTSubscriber* _subscriber; // Remote stream subscriber
NSString* signalReceived; // Last received signal
IBOutlet UITextView* justALabel; // Display area for signals
```

**Critical Configuration (Lines 30-34):**
```objc
static NSString* const kApiKey = @"100";
static NSString* const kSessionId = @"1_MX4xMDB...";
static NSString* const kToken = @"T1==cGFydG5lcl9pZ...";
```

**Important Methods:**

| Method | Line | Purpose |
|--------|------|---------|
| `viewDidLoad` | 41 | Initializes OpenTok session |
| `doConnect` | 78 | Connects to OpenTok session |
| `doPublish` | 94 | Starts publishing local stream |
| `doSubscribe:` | 127 | Subscribes to remote stream |
| `sessionDidConnect:` | 153 | Session connection callback |
| `session:receivedSignalType:fromConnection:withString:` | 211 | Receives signals |
| `sendASignal:` | 327 | Sends encrypted signal via JWT |
| `deCrypt:` | 320 | Decrypts received JWT signal |

### 2. JWT Implementation (JWT.h/m)

**Location:** `Hello-World/JWT.h:1`

**Purpose:** Complete JWT implementation for secure signal encoding/decoding

**Key Methods:**
```objc
// Encoding
+ (NSString *)encodePayload:(NSDictionary *)thePayload
                 withSecret:(NSString *)theSecret;

// Decoding
+ (NSDictionary *)decodeMessage:(NSString *)theMessage
                     withSecret:(NSString *)theSecret;
```

**Secret Key Used:** `"this-is-a-teapot"` (Line 321, 329 in ViewController.m)

### 3. JWT Algorithms

**Supported Algorithms:**
- **JWTAlgorithmHS256**: HMAC SHA-256 (default, most common)
- **JWTAlgorithmHS512**: HMAC SHA-512 (stronger)
- **JWTAlgorithmNone**: Unsigned tokens (not recommended for production)

## Development Workflows

### Setting Up the Development Environment

1. **Prerequisites:**
   - Xcode 5.0+ (supports iOS 6.0 target)
   - CocoaPods installed
   - macOS development machine

2. **Initial Setup:**
   ```bash
   # Clone the repository
   git clone <repository-url>
   cd 1.Hello-World

   # Install dependencies
   pod install

   # Open workspace (NOT .xcodeproj)
   open Hello-World.xcworkspace
   ```

3. **Configuration:**
   - Obtain OpenTok credentials from https://dashboard.tokbox.com/projects
   - Update `ViewController.m` lines 30-34 with your:
     - API Key
     - Session ID
     - Token

### Building the Project

```bash
# Build from command line (if needed)
xcodebuild -workspace Hello-World.xcworkspace \
           -scheme Hello-World \
           -sdk iphonesimulator \
           -configuration Debug
```

### Running Tests

```bash
# Run unit tests
xcodebuild test -workspace Hello-World.xcworkspace \
                -scheme Hello-World \
                -sdk iphonesimulator
```

### Git Workflow

**Current Branch:** `claude/claude-md-mhzwat1hmh1wd2nm-019S6jVr8hzSmWcnYvvtAJZe`

**Important Git Practices:**
1. Always develop on the designated feature branch
2. Branch naming: Must start with `claude/` and include session ID
3. Use descriptive commit messages
4. Push with: `git push -u origin <branch-name>`
5. Retry network failures up to 4 times with exponential backoff

## Code Conventions

### Objective-C Style
- **File Naming:** PascalCase for classes, hyphenated for resources
- **Methods:** camelCase with descriptive names
- **Properties:** Use `@property` with appropriate attributes
- **Private Variables:** Use underscore prefix (e.g., `_session`)
- **Constants:** Use `static` with `k` prefix (e.g., `kApiKey`)

### Memory Management
- This project uses **Manual Reference Counting (MRC)** or early **ARC**
- Be careful with retain cycles in delegate patterns
- Clean up views and objects in cleanup methods

### Delegate Implementation
```objc
@interface ViewController ()
<OTSessionDelegate, OTSubscriberKitDelegate, OTPublisherDelegate>
@end
```

### Error Handling Pattern
```objc
OTError *error = nil;
[_session connectWithToken:kToken error:&error];
if (error) {
    [self showAlert:[error localizedDescription]];
}
```

## Key Conventions for AI Assistants

### When Working with This Codebase

1. **DO:**
   - Preserve the existing Objective-C patterns and style
   - Use the established delegate patterns
   - Maintain compatibility with iOS 6.0+
   - Test both iPhone and iPad storyboards when making UI changes
   - Use the existing JWT implementation for signal encryption
   - Follow the cleanup pattern for publishers/subscribers
   - Update both .h and .m files for interface changes

2. **DON'T:**
   - Don't break the OpenTok session lifecycle
   - Don't hardcode credentials in commits (use placeholders)
   - Don't remove error handling
   - Don't mix Swift code (this is an Objective-C project)
   - Don't modify the JWT algorithm without understanding security implications
   - Don't commit `.xcuserdata` or other Xcode user-specific files

3. **SECURITY CONSIDERATIONS:**
   - The JWT secret (`"this-is-a-teapot"`) is for demo purposes only
   - API keys and tokens should be rotated for production
   - Consider moving credentials to a configuration file or environment
   - Validate all received signals before processing

4. **TESTING CHECKLIST:**
   - [ ] App builds without errors
   - [ ] OpenTok session connects successfully
   - [ ] Publisher stream appears
   - [ ] Subscriber stream appears (if multiple participants)
   - [ ] Signals can be sent and received
   - [ ] JWT encryption/decryption works correctly
   - [ ] UI displays signals properly
   - [ ] Both iPhone and iPad interfaces work
   - [ ] No memory leaks in session lifecycle

### Common Modifications

#### Updating OpenTok Credentials
**File:** `Hello-World/ViewController.m:30-34`
```objc
static NSString* const kApiKey = @"YOUR_API_KEY";
static NSString* const kSessionId = @"YOUR_SESSION_ID";
static NSString* const kToken = @"YOUR_TOKEN";
```

#### Changing JWT Secret
**File:** `Hello-World/ViewController.m:321,329`
```objc
// Update both encode and decode calls
[JWT encodePayload:payload withSecret:@"your-new-secret"];
[JWT decodeMessage:message withSecret:@"your-new-secret"];
```

#### Modifying Signal Format
**File:** `Hello-World/ViewController.m:329`
```objc
NSDictionary *payload = @{
    @"signal": @"your message",
    @"timestamp": @(time),
    @"customField": @"value"
};
```

### Debugging Tips

1. **OpenTok Issues:**
   - Check console logs for OpenTok error messages
   - Verify credentials are correct and not expired
   - Ensure internet connectivity
   - Check delegate method implementations

2. **JWT Issues:**
   - Verify secret matches on encode/decode
   - Check Base64 padding is correct
   - Validate payload structure

3. **UI Issues:**
   - Check IBOutlet connections in storyboard
   - Verify frame calculations for different devices
   - Test on both iPhone and iPad simulators

## Resource Files

### Storyboards
- **Main_iPhone.storyboard**: iPhone-specific interface (portrait preferred)
- **Main_iPad.storyboard**: iPad-specific interface (all orientations)

### Configuration
- **Hello-World-Info.plist**: Bundle configuration
  - Bundle identifier: `com.tokbox.${PRODUCT_NAME:rfc1034identifier}`
  - Version: 1.0
  - Supported orientations defined for iPhone and iPad

### Localization
- Currently supports: English (en.lproj)
- To add languages: Create new .lproj directories and localize strings

## Dependencies Management

### CocoaPods
**File:** `Podfile`
```ruby
platform :ios, '6.0'
pod 'OpenTok'
```

### Updating Dependencies
```bash
# Update to latest compatible versions
pod update

# Install after Podfile changes
pod install

# Always commit Podfile.lock after changes
git add Podfile.lock
git commit -m "Update dependencies"
```

## Build Configuration

### Supported Platforms
- iPhone (portrait orientation only, enforced in code)
- iPad (all orientations supported)

### Required Device Capabilities
- ARMv7 processor
- Camera (for video publishing)
- Microphone (for audio publishing)

### Deployment Target
- Minimum: iOS 6.0
- Note: iOS 6.0 is extremely outdated; consider upgrading for modern projects

## Known Limitations & Technical Debt

1. **Outdated iOS Target**: iOS 6.0 is no longer supported by Apple
2. **Hardcoded Credentials**: API keys and secrets are in source code
3. **Limited Error Handling**: Some edge cases not covered
4. **No Keychain Integration**: Credentials not stored securely
5. **Single View Architecture**: No navigation or multiple screens
6. **Manual JWT Implementation**: Could use established libraries
7. **No Unit Tests for Core Functionality**: Test file exists but minimal implementation

## Future Enhancement Opportunities

1. Migrate to modern iOS versions (iOS 13+)
2. Convert to Swift or Swift/Objective-C hybrid
3. Implement secure credential storage
4. Add comprehensive unit and UI tests
5. Implement proper MVC or MVVM architecture
6. Add multiple views/screens with navigation
7. Improve error handling and user feedback
8. Add support for multiple concurrent subscribers
9. Implement signal history/persistence
10. Add user authentication flow

## Quick Reference

### File Locations for Common Tasks

| Task | File(s) | Lines |
|------|---------|-------|
| OpenTok credentials | ViewController.m | 30-34 |
| JWT secret | ViewController.m | 321, 329 |
| UI layout | Main_iPhone.storyboard, Main_iPad.storyboard | - |
| App configuration | Hello-World-Info.plist | - |
| Dependencies | Podfile | 1-3 |
| Session setup | ViewController.m | 41-52 |
| Signal sending | ViewController.m | 327-332 |
| Signal receiving | ViewController.m | 211-218 |
| Signal decryption | ViewController.m | 320-325 |

### Important URLs
- OpenTok Dashboard: https://dashboard.tokbox.com/projects
- OpenTok Documentation: https://tokbox.com/developer/
- CocoaPods: https://cocoapods.org/

## Contact & Support

**Copyright:** TokBox, Inc. (2013)
**Original Use Case:** PSE/AWT demonstration project

For issues with:
- **OpenTok SDK**: Refer to Vonage Video API documentation
- **JWT Implementation**: Review JWT.h/m and related algorithm files
- **iOS Development**: Check Apple Developer documentation

---

**Last Updated:** 2025-11-15
**Document Version:** 1.0
**AI Assistant:** This document is designed for AI assistants to understand and work effectively with this codebase. Follow the conventions and patterns established here for consistency.
