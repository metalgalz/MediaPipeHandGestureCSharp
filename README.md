# Real-Time Hand Gesture Control for Windows

This project allows you to control your Windows computer using hand gestures captured through your webcam. By showing specific sequences of finger counts to the camera, you can launch applications like Notepad, Calculator, or even display the current time.

It uses a powerful combination of a web-based frontend for hand detection and a C\# backend to interpret these gestures and execute commands on your system.

 (*Pro Tip: Replace this with an actual screenshot of your application in action.*)

-----

## Features

  - **Real-Time Hand Tracking**: Utilizes Google's MediaPipe Hand Landmarker to accurately detect and track hand landmarks in real-time.
  - **Accurate Finger Counting**: A custom algorithm robustly counts the number of raised fingers on one or two hands.
  - **Sequential Gesture Recognition**: Define sequences of gestures (e.g., show 1 finger, then 2, then 3) to trigger specific actions, preventing accidental command execution.
  - **Extensible C\# Backend**: Easily add new gesture sequences and link them to any action you can code in C\#, such as opening applications, running scripts, or interacting with the OS.
  - **Modern UI**: A sleek, responsive web interface built with Tailwind CSS that looks great and is easy to understand.
  - **Efficient Communication**: The web frontend communicates seamlessly with the C\# host application via WebView2's `postMessage` API, ensuring low-latency communication.

-----

## How It Works

The application's architecture is split into two main components:

1.  **Frontend (HTML/JavaScript)**:

      * This is the `Hand.html` file, which is loaded into a WebView2 control.
      * It uses `navigator.mediaDevices.getUserMedia` to access the webcam feed.
      * **MediaPipe Hand Landmarker** processes the video stream to detect the 21 key points on each hand.
      * A custom JavaScript function, `countRaisedFingers`, analyzes the coordinates of these key points to determine how many fingers are extended. The logic is designed to be robust for both left and right hands.
      * The final finger count is then sent to the C\# backend using `window.chrome.webview.postMessage()`.

2.  **Backend (C\# WinForms with WebView2)**:

      * The C\# application hosts the WebView2 browser control.
      * It listens for messages from the JavaScript frontend via the `CoreWebView2_WebMessageReceived` event.
      * A `ProcessGestureForApp` method receives the finger count and maintains a short history of recent gestures.
      * A `Dictionary<List<int>, Action>` named `gestureActions` stores the predefined gesture sequences (the key) and the corresponding C\# methods to execute (the value).
      * When the gesture history matches a predefined sequence, the associated `Action` is invoked, and the history is cleared to wait for the next gesture command.

-----

## Getting Started

### Prerequisites

  * **Windows 10/11**: This is a Windows-based application.
  * **.NET Desktop Development Environment**: Make sure you have Visual Studio installed with the .NET desktop development workload.
  * **WebView2 Runtime**: The WebView2 runtime must be installed on the target machine. It comes pre-installed on modern Windows 11 systems, but you can download it [here](https://developer.microsoft.com/en-us/microsoft-edge/webview2/).
  * **A Webcam**: Required for hand gesture detection.

### Installation & Running the Application

1.  **Clone the repository:**
    ```bash
    git clone https://github.com/your-username/your-repo-name.git
    ```
2.  **Open the solution in Visual Studio:**
      * Navigate to the cloned directory.
      * Open the `.sln` file.
3.  **Restore NuGet Packages:**
      * Right-click on the solution in the Solution Explorer and select "Restore NuGet Packages." This will install the necessary libraries, including `Microsoft.Web.WebView2`.
4.  **Set the HTML file path:**
      * Ensure the `Hand.html` file is located in the correct output directory. In Visual Studio, select the `Hand.html` file in the Solution Explorer and in the **Properties** window, set **Copy to Output Directory** to **Copy if newer**.
5.  **Run the project:**
      * Press **F5** or click the "Start" button in Visual Studio to build and run the application.
      * The application will launch, and you will be prompted to grant camera access in the window.
      * Once you grant permission, the webcam feed will appear. Show your hand to the camera to begin.

-----

## Customization: Adding New Gestures

It's very easy to add your own gesture commands.

1.  **Open `FormFingerDetection.cs`** in Visual Studio.
2.  **Navigate to the `InitializeGestures` method.**
3.  **Uncomment or add new entries** to the `gestureActions` dictionary.

The dictionary's key is a `List<int>` representing the sequence of finger counts, and the value is the `Action` (a method) to execute.

### Example:

To create a new gesture that opens **Microsoft Paint** by showing **2 fingers** and then a **fist (0 fingers)**, you would add the following line inside the `gestureActions` dictionary initialization:

```csharp
private void InitializeGestures()
{
    gestureActions = new Dictionary<List<int>, Action>
    {
        // ... existing gestures

        // New Gesture: Show 2 fingers, then a fist (0) to open MS Paint
        { new List<int> { 2, 0 }, () => OpenApp("mspaint.exe", "MS Paint") },

        // You can also link to your own custom methods
        { new List<int> { 4, 5 }, () => ShowCustomMessage() }
    };
}

// Don't forget to define the custom method if you use one
private void ShowCustomMessage()
{
    MessageBox.Show("You triggered a custom action!", "Gesture Detected");
}
```

-----

## Dependencies

  * [**MediaPipe Tasks Vision for Web**](https://developers.google.com/mediapipe/solutions/vision/hand_landmarker/web_js): For the core hand detection and landmarking.
  * [**Microsoft WebView2**](https://learn.microsoft.com/en-us/microsoft-edge/webview2/): To host the web content within the C\# WinForms application.
  * [**Tailwind CSS**](https://tailwindcss.com/): For styling the frontend user interface.
  * [**Google Fonts (Inter)**](https://fonts.google.com/specimen/Inter): Used for the UI text.
