---
templateKey: post
title: usePageVisibility
date: "2021-04-26"
gist: https://gist.github.com/technikhil314/7dba95d1f9a6767b1c05070e7063764e
sandbox: https://codesandbox.io/s/use-page-visibility-mel5h
---

This hook can be used to stop visual computation such as pausing a video or carousel when user is no more active on your react app. and again resuming the video or carousel when user focuses back on your app. <br/>
It uses [Page Visibility API](https://developer.mozilla.org/en-US/docs/Web/API/Page_Visibility_API) which works in tab switching in same browser and also uses `window.onblur`/`window.onfocus` events when user switches away from browser window.

```jsx
import { useEffect, useRef, useState } from "react";

function usePageVisibility() {
  const [isVisible, setIsVisible] = useState(true);
  useEffect(() => {
    let hidden, visibilityChange;
    if (typeof document.hidden !== "undefined") {
      // Opera 12.10 and Firefox 18 and later support
      hidden = "hidden";
      visibilityChange = "visibilitychange";
    } else if (typeof document.msHidden !== "undefined") {
      hidden = "msHidden";
      visibilityChange = "msvisibilitychange";
    } else if (typeof document.webkitHidden !== "undefined") {
      hidden = "webkitHidden";
      visibilityChange = "webkitvisibilitychange";
    }
    const handleVisibilityChange = (e) => {
      e.stopPropagation();
      if (document[hidden]) {
        setIsVisible(false);
      } else {
        setIsVisible(true);
      }
    };
    window.onblur = (e) => {
      setIsVisible(false);
    };
    window.onfocus = (e) => {
      setIsVisible(true);
    };
    document.addEventListener(visibilityChange, handleVisibilityChange, true);
    return () => {
      document.removeEventListener(visibilityChange, handleVisibilityChange);
    };
  }, []);
  return isVisible;
}

export default function App() {
  const isPageVisible = usePageVisibility();
  const videoRef = useRef();
  useEffect(() => {
    videoRef.current.muted = false;
  }, []);
  useEffect(() => {
    const functionName = isPageVisible ? "play" : "pause";
    videoRef.current[functionName]();
  }, [isPageVisible]);
  return (
    <div className="App">
      <video
        ref={videoRef}
        src="https://www.learningcontainer.com/wp-content/uploads/2020/05/sample-mp4-file.mp4"
        autoplay
        muted
        height="200"
        width="300"
        id="video"
        controls
        loop
      ></video>
    </div>
  );
}
```
