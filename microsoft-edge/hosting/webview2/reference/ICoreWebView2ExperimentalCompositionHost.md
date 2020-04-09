---
description: Host web content in your Win32 app with the Microsoft Edge WebView2 control
title: Microsoft Edge WebView2 for Win32 apps
author: MSEdgeTeam
ms.author: msedgedevrel
ms.date: 04/08/2020
ms.topic: reference
ms.prod: microsoft-edge
ms.technology: webview
keywords: IWebView2, IWebView2WebView, webview2, webview, win32 apps, win32, edge, ICoreWebView2, ICoreWebView2Host, browser control, edge html
---

# interface ICoreWebView2ExperimentalCompositionHost 

```
interface ICoreWebView2ExperimentalCompositionHost
  : public IUnknown
```

This interface is an extension of the ICoreWebView2Host interface to support visual hosting.

## Summary

 Members                        | Descriptions
--------------------------------|---------------------------------------------
[get_UIAProvider](#get_uiaprovider) | Returns the UI Automation Provider for the WebView.
[get_RootVisualTarget](#get_rootvisualtarget) | The RootVisualTarget is a visual in the hosting app's visual tree.
[put_RootVisualTarget](#put_rootvisualtarget) | Set the RootVisualTarget property.
[SendMouseInput](#sendmouseinput) | If eventKind is CORE_WEBVIEW2_MOUSE_EVENT_KIND_HORIZONTAL_WHEEL or CORE_WEBVIEW2_MOUSE_EVENT_KIND_WHEEL, then mouseData specifies the amount of wheel movement.
[CreateCoreWebView2PointerInfoFromPointerId](#createcorewebview2pointerinfofrompointerid) | A helper function to convert a pointerId received from the system into an ICoreWebView2ExperimentalPointerInfo.
[SendPointerInput](#sendpointerinput) | SendPointerInput accepts touch or pen pointer input of types defined in CORE_WEBVIEW2_POINTER_EVENT_KIND.
[get_Cursor](#get_cursor) | The current cursor that WebView thinks it should be.
[add_CursorChanged](#add_cursorchanged) | Add an event handler for the CursorChanged event.
[remove_CursorChanged](#remove_cursorchanged) | Remove an event handler previously added with add_CursorChanged.
[CORE_WEBVIEW2_MOUSE_EVENT_KIND](#core_webview2_mouse_event_kind) | Mouse event type used by SendMouseInput to convey the type of mouse event being sent to WebView.
[CORE_WEBVIEW2_MOUSE_EVENT_VIRTUAL_KEYS](#core_webview2_mouse_event_virtual_keys) | Mouse event virtual keys associated with a CORE_WEBVIEW2_MOUSE_EVENT_KIND for SendMouseInput.
[CORE_WEBVIEW2_POINTER_EVENT_KIND](#core_webview2_pointer_event_kind) | Pointer event type used by SendPointerInput to convey the type of pointer event being sent to WebView.
[CORE_WEBVIEW2_MATRIX_4X4](#core_webview2_matrix_4x4) | Matrix that represents a 3D transform.

An object implementing the ICoreWebView2ExperimentalCompositionHost interface will also implement ICoreWebView2Host. Callers are expected to use ICoreWebView2Host for resizing, visibility, focus, etc, and then use ICoreWebView2ExperiementalCompositionHost to connect to a composition tree and provide input meant for the WebView.

## Members

#### get_UIAProvider 

Returns the UI Automation Provider for the WebView.

> public HRESULT [get_UIAProvider](#get_uiaprovider)(IUnknown ** provider)

If the rendering mode is CORE_WEBVIEW2_RENDERING_MODE_WINDOWED, then this API will return a null provider.

#### get_RootVisualTarget 

The RootVisualTarget is a visual in the hosting app's visual tree.

> public HRESULT [get_RootVisualTarget](#get_rootvisualtarget)(IUnknown ** target)

This visual is where the WebView will connect its visual tree. The app uses this visual to position the WebView within the app. The app still needs to use the Bounds property to size the WebView. The RootVisualTarget property can be an IDCompositionVisual or a Windows::UI::Composition::ContainerVisual. WebView will connect its visual tree to the provided visual before returning from the property setter. The app needs to commit on its device setting the RootVisualTarget property. The RootVisualTarget property supports being set to nullptr to disconnect the WebView from the app's visual tree.

#### put_RootVisualTarget 

Set the RootVisualTarget property.

> public HRESULT [put_RootVisualTarget](#put_rootvisualtarget)(IUnknown * target)

#### SendMouseInput 

If eventKind is CORE_WEBVIEW2_MOUSE_EVENT_KIND_HORIZONTAL_WHEEL or CORE_WEBVIEW2_MOUSE_EVENT_KIND_WHEEL, then mouseData specifies the amount of wheel movement.

> public HRESULT [SendMouseInput](#sendmouseinput)(CORE_WEBVIEW2_MOUSE_EVENT_KIND eventKind,CORE_WEBVIEW2_MOUSE_EVENT_VIRTUAL_KEYS virtualKeys,UINT32 mouseData,POINT point)

A positive value indicates that the wheel was rotated forward, away from the user; a negative value indicates that the wheel was rotated backward, toward the user. One wheel click is defined as WHEEL_DELTA, which is 120. If eventKind is CORE_WEBVIEW2_MOUSE_EVENT_KIND_X_BUTTON_DOUBLE_CLICK CORE_WEBVIEW2_MOUSE_EVENT_KIND_X_BUTTON_DOWN, or CORE_WEBVIEW2_MOUSE_EVENT_KIND_X_BUTTON_UP, then mouseData specifies which X buttons were pressed or released. This value should be 1 if the first X button is pressed/released and 2 if the second X button is pressed/released. If eventKind is CORE_WEBVIEW2_MOUSE_EVENT_KIND_LEAVE, then virtualKeys, mouseData, and point should all be zero. If eventKind is any other value, then mouseData should be zero. Point is expected to be in the client coordinate space of the WebView. To track mouse events that start in the WebView and can potentially move outside of the WebView and host application, calling SetCapture and ReleaseCapture is recommended. To dismiss hover popups, it is also recommended to send WM_MOUSELEAVE messages. 
```cpp
```

#### CreateCoreWebView2PointerInfoFromPointerId 

A helper function to convert a pointerId received from the system into an ICoreWebView2ExperimentalPointerInfo.

> public HRESULT [CreateCoreWebView2PointerInfoFromPointerId](#createcorewebview2pointerinfofrompointerid)(UINT pointerId,HWND parentWindow,struct CORE_WEBVIEW2_MATRIX_4X4 transform,ICoreWebView2ExperimentalPointerInfo ** pointerInfo)

parentWindow is the HWND that contains the webview. This can be any HWND in the hwnd tree that contains the webview. The CORE_WEBVIEW2_MATRIX_4X4 is the transfrom from that HWND to the webview. The returned ICoreWebView2ExperimentalPointerInfo is used in SendPointerInfo. The pointer type must be either pen or touch or the function will fail.

#### SendPointerInput 

SendPointerInput accepts touch or pen pointer input of types defined in CORE_WEBVIEW2_POINTER_EVENT_KIND.

> public HRESULT [SendPointerInput](#sendpointerinput)(CORE_WEBVIEW2_POINTER_EVENT_KIND eventType,ICoreWebView2ExperimentalPointerInfo * pointerInfo)

Any pointer input from the system must be converted into an ICoreWebView2ExperimentalPointerInfo first.

#### get_Cursor 

The current cursor that WebView thinks it should be.

> public HRESULT [get_Cursor](#get_cursor)(HCURSOR * cursor)

The cursor should be set in WM_SETCURSOR through ::SetCursor or set on the corresponding parent/ancestor HWND of the WebView through ::SetClassLongPtr. The HCURSOR can be freed so CopyCursor/DestroyCursor is recommended to keep your own copy if you are doing more than immediately setting the cursor.

#### add_CursorChanged 

Add an event handler for the CursorChanged event.

> public HRESULT [add_CursorChanged](#add_cursorchanged)(ICoreWebView2ExperimentalCursorChangedEventHandler * eventHandler,EventRegistrationToken * token)

The event fires when WebView thinks the cursor should be changed. For example, when the mouse cursor is currently the default cursor but is then moved over text, it may try to change to the IBeam cursor.

```cpp
```

#### remove_CursorChanged 

Remove an event handler previously added with add_CursorChanged.

> public HRESULT [remove_CursorChanged](#remove_cursorchanged)(EventRegistrationToken token)

#### CORE_WEBVIEW2_MOUSE_EVENT_KIND 

Mouse event type used by SendMouseInput to convey the type of mouse event being sent to WebView.

> enum [CORE_WEBVIEW2_MOUSE_EVENT_KIND](#core_webview2_mouse_event_kind)

 Values                         | Descriptions
--------------------------------|---------------------------------------------
CORE_WEBVIEW2_MOUSE_EVENT_KIND_HORIZONTAL_WHEEL            | Mouse horizonal wheel scroll event, WM_MOUSEHWHEEL.
CORE_WEBVIEW2_MOUSE_EVENT_KIND_LEFT_BUTTON_DOUBLE_CLICK            | Left button double click mouse event, WM_LBUTTONDBLCLK.
CORE_WEBVIEW2_MOUSE_EVENT_KIND_LEFT_BUTTON_DOWN            | Left button down mouse event, WM_LBUTTONDOWN.
CORE_WEBVIEW2_MOUSE_EVENT_KIND_LEFT_BUTTON_UP            | Left button up mouse event, WM_LBUTTONUP.
CORE_WEBVIEW2_MOUSE_EVENT_KIND_LEAVE            | Mouse leave event, WM_MOUSELEAVE.
CORE_WEBVIEW2_MOUSE_EVENT_KIND_MIDDLE_BUTTON_DOUBLE_CLICK            | Middle button double click mouse event, WM_MBUTTONDBLCLK.
CORE_WEBVIEW2_MOUSE_EVENT_KIND_MIDDLE_BUTTON_DOWN            | Middle button down mouse event, WM_MBUTTONDOWN.
CORE_WEBVIEW2_MOUSE_EVENT_KIND_MIDDLE_BUTTON_UP            | Middle button up mouse event, WM_MBUTTONUP.
CORE_WEBVIEW2_MOUSE_EVENT_KIND_MOVE            | Mouse move event, WM_MOUSEMOVE.
CORE_WEBVIEW2_MOUSE_EVENT_KIND_RIGHT_BUTTON_DOUBLE_CLICK            | Right button double click mouse event, WM_RBUTTONDBLCLK.
CORE_WEBVIEW2_MOUSE_EVENT_KIND_RIGHT_BUTTON_DOWN            | Right button down mouse event, WM_RBUTTONDOWN.
CORE_WEBVIEW2_MOUSE_EVENT_KIND_RIGHT_BUTTON_UP            | Right button up mouse event, WM_RBUTTONUP.
CORE_WEBVIEW2_MOUSE_EVENT_KIND_WHEEL            | Mouse wheel scroll event, WM_MOUSEWHEEL.
CORE_WEBVIEW2_MOUSE_EVENT_KIND_X_BUTTON_DOUBLE_CLICK            | First or second X button double click mouse event, WM_XBUTTONDBLCLK.
CORE_WEBVIEW2_MOUSE_EVENT_KIND_X_BUTTON_DOWN            | First or second X button down mouse event, WM_XBUTTONDOWN.
CORE_WEBVIEW2_MOUSE_EVENT_KIND_X_BUTTON_UP            | First or second X button up mouse event, WM_XBUTTONUP.

The values of this enum align with the matching WM_* window messages.

#### CORE_WEBVIEW2_MOUSE_EVENT_VIRTUAL_KEYS 

Mouse event virtual keys associated with a CORE_WEBVIEW2_MOUSE_EVENT_KIND for SendMouseInput.

> enum [CORE_WEBVIEW2_MOUSE_EVENT_VIRTUAL_KEYS](#core_webview2_mouse_event_virtual_keys)

 Values                         | Descriptions
--------------------------------|---------------------------------------------
CORE_WEBVIEW2_MOUSE_EVENT_VIRTUAL_KEYS_NONE            | No additional keys pressed.
CORE_WEBVIEW2_MOUSE_EVENT_VIRTUAL_KEYS_LEFT_BUTTON            | Left mouse button is down, MK_LBUTTON.
CORE_WEBVIEW2_MOUSE_EVENT_VIRTUAL_KEYS_RIGHT_BUTTON            | Right mouse button is down, MK_RBUTTON.
CORE_WEBVIEW2_MOUSE_EVENT_VIRTUAL_KEYS_SHIFT            | SHIFT key is down, MK_SHIFT.
CORE_WEBVIEW2_MOUSE_EVENT_VIRTUAL_KEYS_CONTROL            | CTRL key is down, MK_CONTROL.
CORE_WEBVIEW2_MOUSE_EVENT_VIRTUAL_KEYS_MIDDLE_BUTTON            | Middle mouse button is down, MK_MBUTTON.
CORE_WEBVIEW2_MOUSE_EVENT_VIRTUAL_KEYS_X_BUTTON1            | First X button is down, MK_XBUTTON1.
CORE_WEBVIEW2_MOUSE_EVENT_VIRTUAL_KEYS_X_BUTTON2            | Second X button is down, MK_XBUTTON2.

These values can be combined into a bit flag if more than one virtual key is pressed for the event. The values of this enum align with the matching MK_* mouse keys.

#### CORE_WEBVIEW2_POINTER_EVENT_KIND 

Pointer event type used by SendPointerInput to convey the type of pointer event being sent to WebView.

> enum [CORE_WEBVIEW2_POINTER_EVENT_KIND](#core_webview2_pointer_event_kind)

 Values                         | Descriptions
--------------------------------|---------------------------------------------
CORE_WEBVIEW2_POINTER_EVENT_KIND_ACTIVATE            | Corresponds to WM_POINTERACTIVATE.
CORE_WEBVIEW2_POINTER_EVENT_KIND_DOWN            | Corresponds to WM_POINTERDOWN.
CORE_WEBVIEW2_POINTER_EVENT_KIND_ENTER            | Corresponds to WM_POINTERENTER.
CORE_WEBVIEW2_POINTER_EVENT_KIND_LEAVE            | Corresponds to WM_POINTERLEAVE.
CORE_WEBVIEW2_POINTER_EVENT_KIND_UP            | Corresponds to WM_POINTERUP.
CORE_WEBVIEW2_POINTER_EVENT_KIND_UPDATE            | Corresponds to WM_POINTERUPDATE.

The values of this enum align with the matching WM_POINTER* window messages.

#### CORE_WEBVIEW2_MATRIX_4X4 

Matrix that represents a 3D transform.

> typedef [CORE_WEBVIEW2_MATRIX_4X4](#core_webview2_matrix_4x4)

This transform is used to represent the WebView2's current transform in SetHostTransform and used to calculate correct coordinates when calling CreateCoreWebView2PointerInfoFromPointerId. This is equivalent to a D2D1_MATRIX_4X4_F

