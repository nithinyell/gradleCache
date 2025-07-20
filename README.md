```

import { testSaga } from 'redux-saga-test-plan';
import * as saga from '../saga.voidCheque';
import { actions } from '../../actions/voidCheque';

describe('Void Cheque Sagas', () => {
  const mockUserID = 'user123';
  const mockCustomerID = 'cust456';
  const mockAccessToken = 'token';
  const mockIdToken = 'idToken';
  const mockPrimary = 'CA';
  const mockCountry = 'CA';
  const label = 'VOID_CHEQUE.Cheque';
  const mockAccountDetails = { accountNumber: '123' };
  const voidChequeAccountsResponse = [{ accountNumber: '123' }];
  const paymentInformationResponse = { paymentInstructions: {} };

  test('getAccounts saga - success', () => {
    testSaga(saga.getAccounts)
      .next()
      .select(expect.any(Function))
      .next(mockCustomerID)
      .select(expect.any(Function))
      .next(mockUserID)
      .select(expect.any(Function))
      .next(mockAccessToken)
      .select(expect.any(Function))
      .next(mockIdToken)
      .select(expect.any(Function))
      .next({ primaryRelationship: mockPrimary, countryCode: mockCountry })
      .call(expect.any(Function), mockCustomerID, mockUserID, mockAccessToken, mockIdToken)
      .next(voidChequeAccountsResponse)
      .put(actions.setAccounts(voidChequeAccountsResponse, label))
      .next()
      .isDone();
  });

  test('getAccounts saga - failure', () => {
    const error = new Error('fail');
    testSaga(saga.getAccounts)
      .next()
      .throw(error)
      .put(actions.setAccountsError(true))
      .next()
      .isDone();
  });

  test('getPaymentInformation saga - success', () => {
    testSaga(saga.getPaymentInformation, { accountDetails: mockAccountDetails })
      .next()
      .select(expect.any(Function))
      .next(mockUserID)
      .select(expect.any(Function))
      .next(mockIdToken)
      .select(expect.any(Function))
      .next(mockAccessToken)
      .call(expect.any(Function), mockAccountDetails, mockUserID, mockAccessToken, mockIdToken)
      .next(paymentInformationResponse)
      .put(actions.setPaymentInformation(paymentInformationResponse))
      .next()
      .isDone();
  });

  test('getPaymentInformation saga - failure', () => {
    const error = new Error('fail');
    testSaga(saga.getPaymentInformation, { accountDetails: mockAccountDetails })
      .next()
      .throw(error)
      .put(actions.setPaymentInformationError(true))
      .next()
      .isDone();
  });

  test('printVoidChequeData saga - success', () => {
    testSaga(saga.printVoidChequeData, { accountDetails: mockAccountDetails })
      .next()
      .select(expect.any(Function))
      .next(mockUserID)
      .select(expect.any(Function))
      .next(mockIdToken)
      .select(expect.any(Function))
      .next(mockAccessToken)
      .call(expect.any(Function), mockAccountDetails, mockCustomerID, mockUserID, mockAccessToken, mockIdToken)
      .next({ SECURE: { downloadedPdf: 'base64data' } })
      .put(actions.setVoidChequeData({ downloadedPdf: 'base64data' }))
      .next()
      .isDone();
  });

  test('printVoidChequeData saga - failure', () => {
    const error = new Error('fail');
    testSaga(saga.printVoidChequeData, { accountDetails: mockAccountDetails })
      .next()
      .throw(error)
      .put(actions.setVoidChequeDataError(true))
      .next()
      .isDone();
  });

  test('downloadVoidChequeData saga - success', () => {
    testSaga(saga.downloadVoidChequeData, { accountDetails: mockAccountDetails })
      .next()
      .select(expect.any(Function))
      .next(mockUserID)
      .select(expect.any(Function))
      .next(mockIdToken)
      .select(expect.any(Function))
      .next(mockAccessToken)
      .call(expect.any(Function), mockAccountDetails, mockCustomerID, mockUserID, mockAccessToken, mockIdToken)
      .next()
      .isDone();
  });

  test('downloadVoidChequeData saga - failure', () => {
    const error = new Error('fail');
    testSaga(saga.downloadVoidChequeData, { accountDetails: mockAccountDetails })
      .next()
      .throw(error)
      .next()
      .isDone();
  });
});
```
