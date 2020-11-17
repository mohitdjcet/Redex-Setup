to better view click edit of README.md
# --> Index.js

import React from 'react';
import ReactDOM from 'react-dom';
import store from './store/store';
import { Provider } from 'react-redux';

ReactDOM.render(
    <Provider store={store}>
      <App />
    </Provider>,document.getElementById('root'));
    
    
# -- >store folder-->store.js
 
import { createStore, applyMiddleware } from 'redux';
import rootReducer from './rootReducer.js';
import thunk from 'redux-thunk';
import { composeWithDevTools } from 'redux-devtools-extension';

const store = createStore(rootReducer,
    composeWithDevTools(applyMiddleware(thunk)
    )
);


export default store;

# -- > store folder --> rootREducer.js

import { combineReducers } from 'redux';
import commonReducer from '../components/LoginFlow/reducer';

const rootReducer = combineReducers({

    common: commonReducer,

});

export default rootReducer;


#--> Componet Folder-->reducer,js

const initialState = {
    isLoggedIn: false,
    isForgotPasswordIn: false,
    isConfirmPasswordCheck: false,
    email: "",
    menuItems: [],
    loginErrMessage: "",
    forgotErrMessage: "",
    confirmErrMessage: ""
}

export default (state = initialState, action) => {
    switch (action.type) {
        case 'SWITCH_LOGIN':
            return {
                ...state,
                isLoggedIn: action.payload
            };
        case 'QUINCUS_APP_INIT':
            return {
                ...state,
                isLoggedIn: (window.localStorage.getItem("loginToken") ? true : false)
            };
        case 'FORGOT_PASSWORD':
            return {
                ...state,
                email: action.payload,
                forgotErrMessage: action.payload
            };
        case 'FORGOT_PASS_CHECK':
            return {
                ...state,
                isForgotPasswordIn: action.payload,
            };
            case 'CONFIRM_PASSWORD':
            return {
                ...state,
                confirmErrMessage: action.payload
            };
        case 'CONFIRM_PASS_CHECK':
            return {
                ...state,
                isConfirmPasswordCheck: action.payload,
            };
        case 'ERROR_DATA':
            return {
                ...state,
                loginErrMessage: action.payload
            };
        case 'FILL_MENU_ITEMS':
                return {
                    ...state,
                    menuItems: action.payload
                };
        default:
            return state
    }
}

# -- > action.js

import { loginValidationAPI, logOutAPI, forgotAPI,resetAPI, GetMenuItems } from "../Services/api"

export const loginValidation = (username, password) => {
    return (dispatch) => {
        dispatch({
            type: 'ERROR_DATA',
            payload: ''
        })
        loginValidationAPI(username, password).then(response => {
            window.localStorage.setItem('loginToken', response.data.token)
            dispatch({
                type: 'SWITCH_LOGIN',
                payload: true
            })
        }).catch(err => {
            console.log(err);
            dispatch({
                type: 'ERROR_DATA',
                payload: err.response.data.errors
            })
        })
    }
}

export const logOut = () =>{
    return (dispatch) => {
        logOutAPI().then(response => {
            window.localStorage.removeItem('loginToken');
            dispatch({
                type: 'SWITCH_LOGIN',
                payload: false
            })
        }).catch = (err) => {
            console.log(err);
        }
    }
}

export const forgot = (email) =>{
    return (dispatch) => {
        forgotAPI(email).then(response => {
            dispatch({
                type: 'FORGOT_PASS_CHECK',
                payload: true
            })
        }).catch(err => {
            console.log(err);
            dispatch({
                type: 'FORGOT_PASSWORD',
                payload: err.response.data.message
            })
        })
    }
}


export const reset = (user) =>{
    return (dispatch) => {
        // const token = new URL(window.location).searchParams.get('reset_password_token');
        resetAPI(user).then(response => {
            dispatch({
                type: 'CONFIRM_PASS_CHECK',
                payload: true
            })
        }).catch(err => {
            console.log(err);
            dispatch({
                type: 'CONFIRM_PASSWORD',
                payload: err.response.data.message
            })
        })
    }
}

export const getMenuItems = () =>{
    return (dispatch) => {
        GetMenuItems().then(response => {
            dispatch({
                type: 'FILL_MENU_ITEMS',
                payload: response && response.data ? response.data.filter(x => x.visible) : []
            })
        }).catch(err => {
            console.log(err);
        })
    }
}

# --> container.js--> like resetCOntainer,loginContainer depend on which container is used


import { connect } from 'react-redux';
import Reset from './index';
import {reset} from '../actions';

const mapStateToProps = (state) =>{
    return{
     ...state,
     confirmErrMessage:state.common.confirmErrMessage,
     isConfirmPasswordCheck:state.common.isConfirmPasswordCheck
    }
}

const mapDispatchToProps = (dispatch) => {
    return{
        reset:(reset_password_token,user)=>dispatch(reset(reset_password_token,user))
    }
}

export default connect(mapStateToProps,mapDispatchToProps)(Reset)


# -- > Final -- > then go to Reset/index.js

used like this.props.confirmErrMessage
