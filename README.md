```
import { testSaga } from 'redux-saga-test-plan';
import {
  getAccounts,
  getPaymentInformation,
  printVoidChequeData,
  downloadVoidChequeData,
} from '../../sagas/voidCheque.sagas';
import * as api from '../../../api/voidChequeApi';
import * as actions from '../../actions/voidChequeActions';

describe('voidCheque sagas (no selectors, no redux state)', () => {
  const customerID = '123';
  const userID = 'abc';
  const accessToken = 'mockAccess';
  const idToken = 'mockId';
  const label = 'VOID_CHEQUE.Cheque';
  const accountDetails = { accountNumber: '0001' };
  const mockResponse = { data: 'mockData' };

  test('getAccounts saga success', () => {
    testSaga(getAccounts)
      .next()
      .call(api.sendVoidChequeNavigationActivity, customerID, userID)
      .next()
      .call(api.getAccounts, customerID, userID, accessToken, idToken)
      .next(mockResponse)
      .put(actions.setAccountsFromResponse(mockResponse))
      .next()
      .isDone();
  });

  test('getPaymentInformation saga success', () => {
    testSaga(getPaymentInformation, { accountDetails })
      .next()
      .call(api.getPaymentInformation, customerID, userID, accessToken, idToken, accountDetails)
      .next(mockResponse)
      .put(actions.setPaymentInformationFromResponse(mockResponse))
      .next()
      .isDone();
  });

  test('printVoidChequeData saga success', () => {
    testSaga(printVoidChequeData, { accountDetails })
      .next()
      .call(api.printVoidChequeData, customerID, userID, accessToken, idToken, accountDetails)
      .next(mockResponse)
      .put(actions.setVoidChequePrintResponse(mockResponse))
      .next()
      .isDone();
  });

  test('downloadVoidChequeData saga success', () => {
    testSaga(downloadVoidChequeData, { accountDetails })
      .next()
      .call(api.downloadVoidChequeData, customerID, userID, accessToken, idToken, accountDetails)
      .next(mockResponse)
      .call(expect.any(Function), mockResponse) // e.g. savePDF
      .next()
      .isDone();
  });
});
```
