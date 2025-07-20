```
import { testSaga } from 'redux-saga-test-plan';
import { getAccounts, getPaymentInformation, printVoidChequeData, downloadVoidChequeData } from '../../sagas/voidCheque.sagas';
import * as api from '../../../api/voidChequeApi';
import * as actions from '../../actions/voidChequeActions';
import { call, select } from 'redux-saga/effects';

describe('VoidCheque Sagas', () => {
  const mockState = {
    auth: {
      customerID: '123',
      userID: 'abc',
      idToken: 'idToken',
      oauthAccessToken: 'oauthAccessToken'
    },
    voidCheque: {
      primaryRelationship: 'CA',
      countryCode: 'US'
    }
  };

  const accountDetails = { accountNumber: '0001' };

  describe('getAccounts', () => {
    it('should handle getAccounts success flow', () => {
      testSaga(getAccounts)
        .next()
        .select(expect.any(Function)).next(mockState.auth)
        .select(expect.any(Function)).next(mockState.voidCheque)
        .call(api.sendVoidChequeNavigationActivity, '123', 'abc', 'oauthAccessToken', 'idToken')
        .call(api.getAccounts, '123', 'abc', 'oauthAccessToken', 'idToken')
        .put(actions.setAccountsFromResponse(expect.any(Object)))
        .isDone();
    });
  });

  describe('getPaymentInformation', () => {
    it('should handle success', () => {
      testSaga(getPaymentInformation, { accountDetails })
        .next()
        .select(expect.any(Function)).next(mockState.auth)
        .call(api.getPaymentInformation, '123', 'abc', 'oauthAccessToken', 'idToken', accountDetails)
        .put(actions.setPaymentInformationFromResponse(expect.any(Object)))
        .isDone();
    });
  });

  describe('printVoidChequeData', () => {
    it('should call printVoidCheque API', () => {
      testSaga(printVoidChequeData, { accountDetails })
        .next()
        .select(expect.any(Function)).next(mockState.auth)
        .call(api.printVoidChequeData, '123', 'abc', 'oauthAccessToken', 'idToken', accountDetails)
        .put(actions.setVoidChequePrintResponse(expect.any(Object)))
        .isDone();
    });
  });

  describe('downloadVoidChequeData', () => {
    it('should call downloadVoidCheque API', () => {
      testSaga(downloadVoidChequeData, { accountDetails })
        .next()
        .select(expect.any(Function)).next(mockState.auth)
        .call(api.downloadVoidChequeData, '123', 'abc', 'oauthAccessToken', 'idToken', accountDetails)
        .call(expect.any(Function)) // savePdf call
        .isDone();
    });
  });
});
```
