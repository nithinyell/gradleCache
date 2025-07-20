```
import { testSaga } from 'redux-saga-test-plan';
import {
    getAccountsSaga,
    getPaymentInformationSaga,
    printVoidChequeDataSaga,
    downloadVoidChequeDataSaga,
    getVoidChequeLabel
} from '../voidCheque';
import * as api from '../../api/voidCheque';
import * as actions from '../actions/voidCheque';

describe('voidCheque sagas', () => {
    const customerID = '123';
    const userID = 'abc';
    const accessToken = 'mockAccess';
    const idToken = 'mockId';
    const accountDetails = { accountNumber: '001', accountId: 'acc123' };
    const primaryRelationship = 'primary';
    const countryCode = 'CA';
    const mockResponse = { data: 'mockData' };
    const mockLabel = 'Void Cheque Label';

    beforeEach(() => {
        // Mock the selector responses
        jest.spyOn(select, 'default').mockImplementation((selector) => {
            if (selector === state => state.auth) {
                return { customerID, userID, primaryRelationship, countryCode };
            }
            if (selector === state => state.voidCheque) {
                return { oauthAccessToken: accessToken, idToken };
            }
            return null;
        });
    });

    afterEach(() => {
        select.default.mockRestore();
    });

    test('getAccounts saga success', () => {
        testSaga(getAccountsSaga)
            .next()
            .select(state => state.auth)
            .next({ customerID, userID, primaryRelationship, countryCode })
            .select(state => state.voidCheque)
            .next({ oauthAccessToken: accessToken, idToken })
            .call(api.getAccounts, customerID, userID, accessToken, idToken)
            .next(mockResponse)
            .call(getVoidChequeLabel, primaryRelationship, countryCode)
            .next(mockLabel)
            .call(api.sendNavigationsUserActivity, mockLabel)
            .next()
            .put(actions.setAccounts(mockResponse, mockLabel))
            .next()
            .isDone();
    });

    test('getPaymentInformation saga success', () => {
        testSaga(getPaymentInformationSaga, { accountDetails })
            .next()
            .select(state => state.auth)
            .next({ customerID, userID })
            .select(state => state.voidCheque)
            .next({ oauthAccessToken: accessToken, idToken })
            .call(api.getPaymentInformation, customerID, userID, accessToken, idToken, accountDetails.accountId)
            .next(mockResponse)
            .put(actions.setPaymentInformation(mockResponse))
            .next()
            .isDone();
    });

    test('getAccounts saga failure', () => {
        const error = new Error('Failed');
        testSaga(getAccountsSaga)
            .next()
            .select(state => state.auth)
            .next({ customerID, userID, primaryRelationship, countryCode })
            .select(state => state.voidCheque)
            .next({ oauthAccessToken: accessToken, idToken })
            .call(api.getAccounts, customerID, userID, accessToken, idToken)
            .throw(error)
            .put(actions.setAccountsError(true))
            .next()
            .isDone();
    });
});
```
