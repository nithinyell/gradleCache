```
import { testSaga } from 'redux-saga-test-plan';
import {
  getAccountsSaga,
  getPaymentInformationSaga,
  printVoidChequeDataSaga,
  downloadVoidChequeDataSaga,
} from '../../../src/sagas/voidCheque';
import * as api from '../../../src/api';
import * as actions from '../../../src/actions/voidChequeActions';
import { sendNavigationUserActivity, saveBase64PDF } from '../../../src/utils/utils';

describe('voidCheque sagas', () => {
  const auth = {
    customerID: 'cust1',
    userID: 'user1',
    primaryRelationship: 'USA',
    countryCode: 'USD',
  };

  const voidCheque = {
    oauthAccessToken: 'token123',
    idToken: 'id123',
  };

  // ---------- ✅ getAccountsSaga ----------
  describe('getAccountsSaga', () => {
    const mockResponse = { data: 'mockData' };
    const label = 'VOID_CHEQUE_Cheque';

    test('success', () => {
      testSaga(getAccountsSaga)
        .next()
        .next({ customerID: auth.customerID, userID: auth.userID })
        .next(voidCheque)
        .call(
          api.getAccounts,
          auth.customerID,
          auth.userID,
          voidCheque.oauthAccessToken,
          voidCheque.idToken
        )
        .next(mockResponse)
        .next({
          primaryRelationship: auth.primaryRelationship,
          countryCode: auth.countryCode,
        })
        .call(
          api.getVoidChequeLabel,
          auth.primaryRelationship,
          auth.countryCode
        )
        .next(label)
        .call(sendNavigationUserActivity, label)
        .next()
        .put(actions.setAccountsFromResponse(mockResponse, label))
        .next()
        .isDone();
    });

    test('failure', () => {
      const error = new Error('API failed');
      testSaga(getAccountsSaga)
        .next()
        .next({ customerID: auth.customerID, userID: auth.userID })
        .next(voidCheque)
        .call(
          api.getAccounts,
          auth.customerID,
          auth.userID,
          voidCheque.oauthAccessToken,
          voidCheque.idToken
        )
        .throw(error)
        .put(actions.setAccountsError(true))
        .next()
        .isDone();
    });
  });

  // ---------- ✅ getPaymentInformationSaga ----------
  describe('getPaymentInformationSaga', () => {
    const action = { accountDetails: { accId: '123' } };
    const mockResponse = { data: 'PaymentInfoData' };

    test('success', () => {
      testSaga(getPaymentInformationSaga, action)
        .next()
        .next({ customerID: auth.customerID, userID: auth.userID })
        .next(voidCheque)
        .call(
          api.getPaymentInformation,
          auth.customerID,
          auth.userID,
          voidCheque.oauthAccessToken,
          voidCheque.idToken,
          action.accountDetails
        )
        .next(mockResponse)
        .put(actions.setPaymentInformation(mockResponse))
        .next()
        .isDone();
    });

    test('failure', () => {
      const error = new Error('Something went wrong');
      testSaga(getPaymentInformationSaga, action)
        .next()
        .next({ customerID: auth.customerID, userID: auth.userID })
        .next(voidCheque)
        .throw(error)
        .put(actions.setPaymentInformationError(true))
        .next()
        .isDone();
    });
  });

  // ---------- ✅ printVoidChequeDataSaga ----------
  describe('printVoidChequeDataSaga', () => {
    const action = { accountDetails: { accId: '456' } };
    const mockPrintResponse = { SECURE: { downloadPdf: 'mockPDFdata' } };

    test('success', () => {
      testSaga(printVoidChequeDataSaga, action)
        .next()
        .next({ customerID: auth.customerID, userID: auth.userID })
        .next(voidCheque)
        .call(
          api.printVoidChequeData,
          auth.customerID,
          auth.userID,
          voidCheque.oauthAccessToken,
          voidCheque.idToken,
          action.accountDetails
        )
        .next(mockPrintResponse)
        .call(
          api.downloadVoidChequeData,
          'mockPDFdata'
        )
        .next()
        .isDone();
    });

    test('failure', () => {
      const error = new Error('Print failed');
      testSaga(printVoidChequeDataSaga, action)
        .next()
        .next({ customerID: auth.customerID, userID: auth.userID })
        .next(voidCheque)
        .throw(error)
        .put(actions.setVoidChequeDataError(true))
        .next()
        .isDone();
    });
  });

  // ---------- ✅ downloadVoidChequeDataSaga ----------
  describe('downloadVoidChequeDataSaga', () => {
    const action = { accountDetails: { accId: '789' } };
    const mockDownloadResponse = { SECURE: { downloadPdf: 'base64-pdf' } };

    test('success', () => {
      testSaga(downloadVoidChequeDataSaga, action)
        .next()
        .next({ customerID: auth.customerID, userID: auth.userID })
        .next(voidCheque)
        .call(
          api.downloadVoidChequeData,
          auth.customerID,
          auth.userID,
          voidCheque.oauthAccessToken,
          voidCheque.idToken,
          action.accountDetails
        )
        .next(mockDownloadResponse)
        .call(saveBase64PDF, 'base64-pdf')
        .next()
        .isDone();
    });

    test('failure', () => {
      const error = new Error('Download failed');
      testSaga(downloadVoidChequeDataSaga, action)
        .next()
        .next({ customerID: auth.customerID, userID: auth.userID })
        .next(voidCheque)
        .throw(error)
        .next()
        .isDone();
    });
  });
});
```
