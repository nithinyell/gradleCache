```
test("SET_VOID_CHEQUE_ACCOUNTS_ERROR sets correct error", () => {
  const error = "Account fetch failed";
  const action = { type: types.SET_VOID_CHEQUE_ACCOUNTS_ERROR, error };
  const newState = VoidChequeReducer(initialState, action);
  expect(newState.accountsListError).toEqual(error);
});

test("SET_VOID_CHEQUE_PAYMENT_INFORMATION_ERROR sets correct error", () => {
  const error = "Payment info failed";
  const action = { type: types.SET_VOID_CHEQUE_PAYMENT_INFORMATION_ERROR, error };
  const newState = VoidChequeReducer(initialState, action);
  expect(newState.paymentInformationError).toEqual(error);
});

test("SET_VOID_CHEQUE_PRINT_DATA_ERROR sets correct error", () => {
  const error = "Print data error";
  const action = { type: types.SET_VOID_CHEQUE_PRINT_DATA_ERROR, error };
  const newState = VoidChequeReducer(initialState, action);
  expect(newState.voidChequeDataError).toEqual(error);
});

test("SET_VOID_CHEQUE_TOKENS sets correct token values", () => {
  const action = {
    type: types.SET_VOID_CHEQUE_TOKENS,
    token: "abc",
    tokenType: "Bearer",
    refreshToken: "ref123",
    idToken: "id456",
    accessTokenTimeout: 3600
  };
  const newState = VoidChequeReducer(initialState, action);
  expect(newState.oauthAccessToken).toBe("abc");
  expect(newState.tokenType).toBe("Bearer");
  expect(newState.refreshToken).toBe("ref123");
  expect(newState.idToken).toBe("id456");
  expect(newState.accessTokenTimeout).toBe(3600);
});

```
