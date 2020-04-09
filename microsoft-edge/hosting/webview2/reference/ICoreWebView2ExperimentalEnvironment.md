---
description: Host web content in your Win32 app with the Microsoft Edge WebView2 control
title: Microsoft Edge WebView2 for Win32 apps
author: MSEdgeTeam
ms.author: msedgedevrel
ms.date: 04/09/2020
ms.topic: reference
ms.prod: microsoft-edge
ms.technology: webview
keywords: IWebView2, IWebView2WebView, webview2, webview, win32 apps, win32, edge, ICoreWebView2, ICoreWebView2Host, browser control, edge html
---

# interface ICoreWebView2ExperimentalEnvironment 

> [!NOTE]
> This an experimental API that is shipped with our prerelease SDK version 0.9.480.

```
interface ICoreWebView2ExperimentalEnvironment
  : public IUnknown
```

This interface is an extension of the ICoreWebView2Environment.

## Summary

 Members                        | Descriptions
--------------------------------|---------------------------------------------
[CreateCoreWebView2CompositionHost](#createcorewebview2compositionhost) | Asynchronously create a new WebView for use with visual hosting.
[CreateCoreWebView2PointerInfo](#createcorewebview2pointerinfo) | Create an empty [ICoreWebView2ExperimentalPointerInfo](ICoreWebView2ExperimentalPointerInfo.md).
[GetProviderForHwnd](#getproviderforhwnd) | Returns the UI Automation Provider for the WebView window that corresponds with the given HWND.

An object implementing the ICoreWebView2ExperimentalEnvironment interface will also implement ICoreWebView2Environment.

## Members

#### CreateCoreWebView2CompositionHost 

Asynchronously create a new WebView for use with visual hosting.

> public HRESULT [CreateCoreWebView2CompositionHost](#createcorewebview2compositionhost)(HWND parentWindow,[ICoreWebView2ExperimentalCreateCoreWebView2CompositionHostCompletedHandler](ICoreWebView2ExperimentalCreateCoreWebView2CompositionHostCompletedHandler.md) * handler)

parentWindow is the HWND in which the app will connect the visual tree of the WebView. This will be the HWND that the app will receive pointer/ mouse input meant for the WebView (and will need to use SendMouseInput/ SendPointerInput to forward). If the app moves the WebView visual tree to underneath a different window, then it needs to call put_ParentWindow to update the new parent HWND of the visual tree.

Use put_RootVisualTarget on the created CoreWebView2CompositionHost to provide a visual to host the browser's visual tree.

It is recommended that the application set Application User Model ID for the process or the application window. If none is set, during WebView creation a generated Application User Model ID is set to root window of parentWindow. 
```cpp
// Create or recreate the WebView and its environment.
void AppWindow::InitializeWebView()
{
    // To ensure browser switches get applied correctly, we need to close
    // the existing WebView. This will result in a new browser process
    // getting created which will apply the browser switches.
    CloseWebView();

    LPCWSTR subFolder = nullptr;
    LPCWSTR additionalBrowserSwitches = nullptr;
    auto options = Microsoft::WRL::Make<CoreWebView2EnvironmentOptions>();
    CHECK_FAILURE(options->put_AdditionalBrowserArguments(additionalBrowserSwitches));
    if(!m_language.empty())
        CHECK_FAILURE(options->put_Language(m_language.c_str()));
    HRESULT hr = CreateCoreWebView2EnvironmentWithOptions(
        subFolder, nullptr, options.Get(),
        Callback<ICoreWebView2CreateCoreWebView2EnvironmentCompletedHandler>(
            this, &AppWindow::OnCreateEnvironmentCompleted)
            .Get());
    if (!SUCCEEDED(hr))
    {
        if (hr == HRESULT_FROM_WIN32(ERROR_FILE_NOT_FOUND))
        {
            MessageBox(
                m_mainWindow,
                L"Couldn't find Edge installation. "
                "Do you have a version installed that's compatible with this "
                "WebView2 SDK version?",
                nullptr, MB_OK);
        }
        else
        {
            ShowFailure(hr, L"Failed to create webview environment");
        }
    }
}

// This is the callback passed to CreateWebViewEnvironmnetWithDetails.
// Here we simply create the WebView.
HRESULT AppWindow::OnCreateEnvironmentCompleted(
    HRESULT result, ICoreWebView2Environment* environment)
{
    CHECK_FAILURE(result);

    CHECK_FAILURE(environment->QueryInterface(IID_PPV_ARGS(&m_webViewEnvironment)));
        CHECK_FAILURE(m_webViewEnvironment->CreateCoreWebView2Host(
            m_mainWindow, Callback<ICoreWebView2CreateCoreWebView2HostCompletedHandler>(
                              this, &AppWindow::OnCreateCoreWebView2HostCompleted)
                              .Get()));
    return S_OK;
}
```
 It is recommended that the application handles restart manager messages so that it can be restarted gracefully in the case when the app is using Edge for webview from a certain installation and that installation is being uninstalled. For example, if a user installs Edge from Dev channel and opts to use Edge from that channel for testing the app, and then uninstalls Edge from that channel without closing the app, the app will be restarted to allow uninstallation of the dev channel to succeed. 
```cpp
    case WM_QUERYENDSESSION:
    {
        // yes, we can shut down
        // Register how we might be restarted
        RegisterApplicationRestart(L"--restore", RESTART_NO_CRASH | RESTART_NO_HANG);
        *result = TRUE;
        return true;
    }
    break;
    case WM_ENDSESSION:
    {
        if (wParam == TRUE)
        {
            // save app state and exit.
            PostQuitMessage(0);
            return true;
        }
    }
    break;
```

#### CreateCoreWebView2PointerInfo 

Create an empty [ICoreWebView2ExperimentalPointerInfo](ICoreWebView2ExperimentalPointerInfo.md).

> public HRESULT [CreateCoreWebView2PointerInfo](#createcorewebview2pointerinfo)([ICoreWebView2ExperimentalPointerInfo](ICoreWebView2ExperimentalPointerInfo.md) ** pointerInfo)

The returned [ICoreWebView2ExperimentalPointerInfo](ICoreWebView2ExperimentalPointerInfo.md) needs to be populated with all of the relevant info before calling SendPointerInput.

#### GetProviderForHwnd 

Returns the UI Automation Provider for the WebView window that corresponds with the given HWND.

> public HRESULT [GetProviderForHwnd](#getproviderforhwnd)(HWND hwnd,IUnknown ** provider)

If the WebView was created with rendering mode CORE_WEBVIEW2_RENDERING_MODE_WINDOWED, the returned provider will be null.

