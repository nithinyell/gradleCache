```
test("setVoidChequeDownloadData is called correctly", () => {
  expect(actions.setVoidChequeDownloadData().type).toBe("VOID_CHEQUE/SET_VOID_CHEQUE_DOWNLOAD_DATA");
});

test("setAccessTokenVal is called correctly", () => {
  expect(actions.setAccessTokenVal().type).toBe("VOID_CHEQUE/SET_VOID_CHEQUE_TOKENS");
});

test("resetPaymentInformationState is called correctly", () => {
  expect(actions.resetPaymentInformationState().type).toBe("VOID_CHEQUE/RESET_PAYMENT_INFORMATION_STATE");
});

```
