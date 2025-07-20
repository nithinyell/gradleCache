```
test("GET_VOID_CHEQUE_DOWNLOAD_DATA sets accountDetails correctly", () => {
  const accountDetails = { account: "123456" };
  const action = {
    type: types.GET_VOID_CHEQUE_DOWNLOAD_DATA,
    accountDetails,
  };
  const newState = VoidChequeReducer(initialState, action);
  expect(newState.accountDetails).toEqual(accountDetails);
});

test("RESET_PAYMENT_INFORMATION_STATE resets specific fields", () => {
  const modifiedState = {
    ...initialState,
    paymentInformation: { dummy: true },
    voidChequePrintData: { dummy: true },
    paymentinformationError: true,
    voidChequeDataError: true,
  };

  const action = { type: types.RESET_PAYMENT_INFORMATION_STATE };
  const newState = VoidChequeReducer(modifiedState, action);

  expect(newState.paymentInformation).toBeNull();
  expect(newState.voidChequePrintData).toBeNull();
  expect(newState.paymentinformationError).toBe(false);
  expect(newState.voidChequeDataError).toBe(false);
});

```
