```
import React from 'react';
import { shallow } from 'enzyme';
import AccountsListScreen from '../AccountsListScreen';

describe('AccountsListScreen', () => {
  let wrapper;
  const mockNavigation = {
    goBack: jest.fn(),
    navigate: jest.fn(),
  };

  const mockProps = {
    getAccounts: jest.fn(),
    navigation: mockNavigation,
    voidCheque: {
      accounts: {
        body: {
          SECURE: {
            voidChequeAccounts: [
              { custAccName: 'Test Account', number: '123456' },
              { custAccName: 'Another Account', number: '654321' },
            ],
          },
        },
      },
    },
  };

  beforeEach(() => {
    jest.clearAllMocks();
    wrapper = shallow(<AccountsListScreen {...mockProps} />);
  });

  test('calls getAccounts on componentDidMount', () => {
    expect(mockProps.getAccounts).toHaveBeenCalled();
  });

  test('calls navigation.goBack on handleBack', () => {
    wrapper.instance().handleBack();
    expect(mockNavigation.goBack).toHaveBeenCalled();
  });

  test('renders SearchBar and updates searchTerm on text change', () => {
    const searchBar = shallow(wrapper.instance().renderSearchBar()).find('SearchBar');
    expect(searchBar.prop('value')).toBe('');
    searchBar.prop('onChangeText')('void');
    expect(wrapper.state('searchTerm')).toBe('void');
  });

  test('clears searchTerm on onClosePress', () => {
    const searchBar = shallow(wrapper.instance().renderSearchBar()).find('SearchBar');
    wrapper.setState({ searchTerm: 'test' });
    searchBar.prop('onClosePress')();
    expect(wrapper.state('searchTerm')).toBe('');
  });

  test('renders AccountsList with correct data', () => {
    const accountListView = shallow(wrapper.instance().renderAccountList());
    const accountsList = accountListView.find('AccountsList');
    expect(accountsList.exists()).toBe(true);
    expect(accountsList.prop('data')).toEqual(mockProps.voidCheque.accounts.body.SECURE.voidChequeAccounts);
  });

  test('navigates to PaymentInformationScreen on account item press', () => {
    const accountItem = mockProps.voidCheque.accounts.body.SECURE.voidChequeAccounts[0];
    const accountsList = shallow(wrapper.instance().renderAccountList()).find('AccountsList');
    accountsList.prop('onPress')(accountItem);
    expect(mockNavigation.navigate).toHaveBeenCalledWith('VoidChequesStack', {
      screen: 'PaymentInformationScreen',
      params: accountItem,
    });
  });

  test('renders NavigationHeader with translated title', () => {
    const rendered = wrapper.render();
    const header = shallow(rendered.props.children[0].props.children[0]);
    expect(header.prop('navigationTitle')).toBe('VOID_CHEQUE.DownloadVoidCheques');
  });
});


```
