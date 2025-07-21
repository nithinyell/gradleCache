```
beforeEach(() => {
    jest.clearAllMocks();
  });

  test('getAccounts saga success', () => {
    testSaga(getAccountsSaga)
      .next()
      .select()
      .next({ customerID, userID })
      .select()
      .next({ customerID, userID, accessToken, idToken })
      .select()  
      .next({})
      .select()
      .next({ oauthAccessToken: accessToken, idToken })
      .call(api.getAccounts, customerID, userID, accessToken, idToken)
      .next(mockResponse)
      .select()
      .next({ primaryRelationship, countryCode })
      .call(getVoidChequeLabel, primaryRelationship, countryCode)
      .next('mockLabel')
      .call(api.sendNavigationUserActivity, 'mockLabel')
      .next()
      .put(actions.setAccountsFromResponse(mockResponse, 'mockLabel'))
      .next()
      .isDone();
  });

  // Add error handling test if needed
  test('getAccounts saga error', () => {
    const error = new Error('API Error');
    
    testSaga(getAccountsSaga)
      .next()
      .select()
      .next({ customerID, userID })
      .select()
      .next({ customerID, userID, accessToken, idToken })
      .select()  
      .next({})
      .select()
      .next({ oauthAccessToken: accessToken, idToken })
      .call(api.getAccounts, customerID, userID, accessToken, idToken)
      .throw(error)
      .put(actions.setAccountsError(true))
      .next()
      .isDone();
  });
```
