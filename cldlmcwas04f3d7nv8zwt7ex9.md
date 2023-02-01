# Adding React Query to a React Native Project

React Query is a performant and powerful data synchronization tool for React and React Native applications. It gives you declarative, always-up-to-date auto-managed queries and mutations that directly improve both your developer and user experience. It fetches, caches and updates data in your applications without touching any global state. It has a ton of features and benefits that I won't even be able to cover in one blog post but it is a package that is making state management in React and React Native applications extremely easy.

I will attempt to show you how you add this package to a React Native project (that is managed by Expo) and start using it right away in your projects

This process works fine for both new and existing projects, so I won't really start from creating a new project point of view but just point you in the right direction on how to add this package to your project straight away.

The first obvious step is to install the necessary packages that you will need. Below is the list of what you will need to get started

```bash
npm i @tanstack/react-query @react-native-community/netinfo
```

In case you need any additional step in your installation process for any of these packages, here is the link to [Netinfo documentation](https://www.npmjs.com/package/@react-native-community/netinfo) and [react query documentation](https://tanstack.com/query/latest/docs/react/installation)

Next, we are going to create some custom hooks that we will need throughout our project. If you haven't done this already, create a folder called hooks in your root directory and create a file called `useOnlineMagaer.js`. It does not matter what you name this file but it is always good practice to use naming conventions that are easy to read and understand. In that file, add the following block of code

```javascript
import { useEffect } from 'react';
import NetInfo from '@react-native-community/netinfo';
import { onlineManager } from 'react-query';
import { Platform } from 'react-native';

export function useOnlineManager() {
  useEffect(() => {
    if (Platform.OS !== 'web') {
      return NetInfo.addEventListener((state) => {
        onlineManager.setOnline(
          state.isConnected != null &&
            state.isConnected &&
            Boolean(state.isInternetReachable)
        );
      });
    }
  }, []);
}
```

This hook gives us a behaviour similar to what we have on the web where it auto-refreshes whenever we reconnect.

Now we will also add the feature where we can refetch queries again when the App is in focus. To do this simply create another custom hook called `useAppState.js` and paste the below code into it

```javascript
import { useEffect } from 'react';
import { AppState, Platform } from 'react-native';
import { focusManager } from 'react-query';

export function useAppState(onChange) {
  function onAppStateChange(status) {
    if (Platform.OS !== 'web') {
      focusManager.setFocused(status === 'active');
    }
  }
  useEffect(() => {
    const subscription = AppState.addEventListener('change', (status) => {
      if (status !== 'active') {
        return;
      }
      onAppStateChange(status);
      subscription.remove();
    });
  }, [onChange]);
}
```

Two more custom hooks and we will be done. Now you may want to refetch queries when a screen is in focus again(The above code was for when our App is in focus). To do this we will add the below block of code to a new custom hook we will call `useRefreshOnFocus.js`

```javascript
import { useRef, useCallback } from 'react';
import { useFocusEffect } from '@react-navigation/native';

export function useRefreshOnFocus(refetch) {
  const enabledRef = useRef(false);

  useFocusEffect(
    useCallback(() => {
      if (enabledRef.current) {
        refetch();
      } else {
        enabledRef.current = true;
      }
    }, [refetch])
  );
}
```

The last hook is similar to the one above but it accounts for when the user refreshes a screen manually by dragging down the page that is wrapped around one of the react native components that allow data refresh. The name of this hook will be `useRefreshByUser.js` and we will add this block of codes to this file

```javascript
import { useState,  } from 'react';

export function useRefreshByUser(refetch) {
  const [isRefetchingByUser, setIsRefetchingByUser] = useState(false);

  async function refetchByUser() {
    setIsRefetchingByUser(true);

    try {
      await refetch();
    } finally {
      setIsRefetchingByUser(false);
    }
  }

  return {
    isRefetchingByUser,
    refetchByUser,
  };
}
```

Finally, we are going to use all these hooks in our `App.js` file and we are good to go. Below are the lines of codes you will add to your file

```javascript
...
import { NavigationContainer } from '@react-navigation/native';
import {
  QueryClient,
  QueryClientProvider,
  focusManager,
} from '@tanstack/react-query';
import { useAppState } from './hooks/useAppState';
import { useOnlineManager } from './hooks/useOnlineManager';

function onAppStateChange(status) {
    if (Platform.OS !== 'web') {
      focusManager.setFocused(status === 'active');
    }
  }
const queryClient = new QueryClient({
  defaultOptions: { queries: { retry: 2 } },
});

const App = () => {
  useOnlineManager();
  useAppState(onAppStateChange);
  return (
    <QueryClientProvider client={queryClient}>
      <NavigationContainer>
        <Your Screens />
      </NavigationContainer>
    </QueryClientProvider>
  );
};

export default App;
```

And just like that you are set up for success. All the queries in the React Query package are open for you to use for you to create better and more user-friendly applications.  
  
Are they other steps you take in your own project, why don't you share it with the world?