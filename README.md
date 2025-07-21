```
 test('getAccounts saga success', () => {
    testSaga(getAccountsSaga)
      .next()
      .select(state => ({ customerID: state.auth.customerID, userID: state.auth.userID }))
      .next({ customerID, userID })
      .select(state => state.auth)
      .next({ customerID, userID, accessToken, idToken })
      .select(state => state.voidCheque)  
      .next({})
      .select(state => ({ oauthAccessToken: state.auth.oauthAccessToken, idToken: state.auth.idToken }))
      .next({ oauthAccessToken: accessToken, idToken })
      .call(api.getAccounts, customerID, userID, accessToken, idToken)
      .next(mockResponse)
      .select(state => ({ primaryRelationship: state.auth.primaryRelationship, countryCode: state.auth.countryCode }))
      .next({ primaryRelationship, countryCode })
      .call(getVoidChequeLabel, primaryRelationship, countryCode)
      .next('mockLabel')
      .call(api.sendNavigationUserActivity, 'mockLabel')
      .next()
      .put(actions.setAccountsFromResponse(mockResponse, 'mockLabel'))
      .next()
      .isDone();
  });
```
