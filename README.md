```
import React from 'react';
import { shallow } from 'enzyme';
import AccountsListScreen from '../AccountsListScreen';
import PopupMessage from '../../../PopupMessage';
import { MessageTypes } from '../../../utils/enums';

describe('AccountsListScreen', () => {
  let wrapper;
  let mockProps;

  beforeEach(() => {
    mockProps = {
      voidCheque: {
        accounts: [{ accountName: "Test Account", accountNumber: "123456" }],
        accountsListError: false
      },
      getAccounts: jest.fn(),
      getVoidChequeDownloadData: jest.fn(),
      navigation: {
        goBack: jest.fn(),
        navigate: jest.fn()
      }
    };

    wrapper = shallow(<AccountsListScreen {...mockProps} />);
  });

  test('calls getAccounts on componentDidMount', () => {
    expect(mockProps.getAccounts).toHaveBeenCalled();
  });

  test('calls navigation.goBack on handleBack()', () => {
    wrapper.instance().handleBack();
    expect(mockProps.navigation.goBack).toHaveBeenCalled();
  });

  test('renders SearchBar and updates searchTerm on text change', () => {
    const searchBarJSX = wrapper.instance().renderSearchBar();
    const viewWrapper = shallow(searchBarJSX);
    const searchBar = viewWrapper.children().first();

    expect(searchBar).toBeDefined();
    searchBar.prop('onChangeText')('void');
    expect(wrapper.state('searchTerm')).toBe('void');
  });

  test('clears searchTerm on onClosePress()', () => {
    wrapper.setState({ searchTerm: 'test' });
    const searchBarJSX = wrapper.instance().renderSearchBar();
    const viewWrapper = shallow(searchBarJSX);
    const searchBar = viewWrapper.children().first();

    searchBar.prop('onClosePress')();
    expect(wrapper.state('searchTerm')).toBe('');
  });

  test('renders AccountsList with correct data', () => {
    const accountListJSX = shallow(wrapper.instance().renderAccountList());
    const accountList = accountListJSX.find('AccountsList');
    expect(accountList.exists()).toBe(true);
    expect(accountList.prop('data')).toEqual(mockProps.voidCheque.accounts);
  });

  test('navigates to PaymentInformationScreen on account item press', () => {
    const accountItem = mockProps.voidCheque.accounts[0];
    const accountList = shallow(wrapper.instance().renderAccountList()).find('AccountsList');

    accountList.prop('onPress')(accountItem);
    expect(mockProps.navigation.navigate).toHaveBeenCalledWith('VoidChequesStack', {
      screen: 'PaymentInformationScreen',
      params: { accountDetails: accountItem }
    });
  });

  test('renders NavigationHeader with translated title', () => {
    const navHeader = shallow(wrapper.instance().renderSearchBar()).children().first();
    expect(navHeader.exists()).toBe(true);
    expect(navHeader.prop('navigationTitle')).toBe('VOID_CHEQUE.DownloadVoidCheques');
  });

  test('shows API error message when accountsListError is true', () => {
    wrapper.setProps({
      voidCheque: {
        ...mockProps.voidCheque,
        accountsListError: true
      }
    });

    const errorComponent = shallow(wrapper.instance().renderError());
    expect(errorComponent.containsMatchingElement(
      <PopupMessage type={MessageTypes.ERROR}>
        {'VOID_CHEQUE.API_ERROR'}
      </PopupMessage>
    )).toBe(true);
  });

  test('shows empty accounts message when no accounts', () => {
    wrapper.setState({ isEmptyAccountList: true });
    const errorComponent = shallow(wrapper.instance().renderError());
    expect(errorComponent.containsMatchingElement(
      <PopupMessage type={MessageTypes.INFO}>
        {'VOID_CHEQUE.NO_ACCOUNTS_FOUND_ERROR'}
      </PopupMessage>
    )).toBe(true);
  });

  test('shows search error message when no results match search term', () => {
    wrapper.setState({ searchError: true });
    const errorComponent = shallow(wrapper.instance().renderError());
    expect(errorComponent.containsMatchingElement(
      <PopupMessage type={MessageTypes.INFO}>
        {'VOID_CHEQUE.SEARCH_ERROR'}
      </PopupMessage>
    )).toBe(true);
  });
});

```
