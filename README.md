```import { testSaga } from 'redux-saga-test-plan';
import {
  getPaymentInformationSaga,
  printVoidChequeDataSaga,
  downloadVoidChequeDataSaga,
} from '../../../src/sagas/voidCheque';
import * as voidChequeApi from '../../../src/api';
import * as actions from '../../../src/actions/voidChequeActions';
import { saveBase64PDF } from '../../../src/utils/utils';

describe('voidCheque sagas', () => {
  const auth = { customerID: 'cust1', userID: 'user1' };
  const voidCheque = { oauthAccessToken: 'token123', idToken: 'id123' };

  describe('getPaymentInformationSaga', () => {
    const action = { accountDetails: { accId: '123' } };
    const mockResponse = { data: 'PaymentInfoData' };

    test('success', () => {
      testSaga(getPaymentInformationSaga, action)
        .next()
        .next(auth)
        .next(voidCheque)
        .call(
          voidChequeApi.getPaymentInformation,
          'cust1',
          'user1',
          'token123',
          'id123',
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
        .next(auth)
        .next(voidCheque)
        .throw(error)
        .put(actions.setPaymentInformationError(true))
        .next()
        .isDone();
    });
  });

  describe('printVoidChequeDataSaga', () => {
    const action = { accountDetails: { accId: '456' } };
    const mockPrintResponse = { SECURE: { downloadPdf: 'mockPDFdata' } };

    test('success', () => {
      testSaga(printVoidChequeDataSaga, action)
        .next()
        .next(auth)
        .next(voidCheque)
        .call(
          voidChequeApi.printVoidChequeData,
          'cust1',
          'user1',
          'token123',
          'id123',
          action.accountDetails
        )
        .next(mockPrintResponse)
        .call(
          voidChequeApi.downloadVoidChequeData,
          'mockPDFdata'
        )
        .next()
        .isDone();
    });

    test('failure', () => {
      const error = new Error('Print failed');
      testSaga(printVoidChequeDataSaga, action)
        .next()
        .next(auth)
        .next(voidCheque)
        .throw(error)
        .put(actions.setVoidChequeDataError(true))
        .next()
        .isDone();
    });
  });

  describe('downloadVoidChequeDataSaga', () => {
    const action = { accountDetails: { accId: '789' } };
    const mockDownloadResponse = { SECURE: { downloadPdf: 'base64-pdf' } };

    test('success', () => {
      testSaga(downloadVoidChequeDataSaga, action)
        .next()
        .next(auth)
        .next(voidCheque)
        .call(
          voidChequeApi.downloadVoidChequeData,
          'cust1',
          'user1',
          'token123',
          'id123',
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
        .next(auth)
        .next(voidCheque)
        .throw(error)
        .next()
        .isDone();
    });
  });
});
```
