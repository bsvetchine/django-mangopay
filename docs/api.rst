Correspondance to Mangopay Rest API
===================================

This library is a wrapper of the Mangopay API as described `here
<http://docs.mangopay.com/api-references/>`_. Each of the API calls listed here
are described with how to match them.

Access
------

`POST /v2/oauth/token <http://docs.mangopay.com/api-references/authenticating/>`_
*********************************************************************************

The client handles authentication with a token once you have set up your client
as described in the installation instructions. Once confrigured it is easy to
get an instance of it to contect to.

::

    import mangopay.client

    client = get_mangopay_api_client()


Activity, research & lists
--------------------------

None of the API calls listed by Mangopay as falling under the category of "Activity, research & lists" are supported at the moment. It is however possible to call use the mangopay-sdk to call them indirectly. An example is given below.

::

    import mangopay.client

    client = get_mangopay_api_client()
    # GET /Events
    client.events.Get()

Users
-----

All API calls under the "Users" are supported.

`POST /users/natural <http://docs.mangopay.com/api-references/users/natural-users/>`_
*************************************************************************************

To create a natural user object just instantiate an instance of
``MangoPayNaturalUser``, populate the requried fields, and call ``create()`` on
it as shown below. You can also edit the user, just update the the values you want to change in the model and then call ``update()``.

::

    from django.contrib.auth.models import User
    from datetime import date
    from mangopay.models import MangoPayNaturalUser

    user = User.object.get(id=1)

    mangopay_user = MangoPayNaturalUser()
    mangopay_user.user = user
    mangopay_user.country_of_residence = "SE"
    mangopay_user.nationality = "US"
    mangopay_user.birthday = date(1989, 10, 20)
    mangopay_user.save()

    mangopay_user.create()

    mangopay_user.address = "Högbergsgatan 66C"
    mangopay_user.save()

    mangopay_user.update()


`POST /users/legal <http://docs.mangopay.com/api-references/users/legal-users/>`_
*********************************************************************************

Creating and editing a legal user is the same as creating a natural user except Mangopay
required different fields. Instantiate the ``MangoPayLegalUser``, populate the
required fields, and call ``create()`` or ``update()`` on it as shown below.

::

    from django.contrib.auth.models import User
    from datetime import date
    from mangopay.models import MangoPayLegalUser
    from mangopay.constants import BUSINESS

    user = User.object.get(id=1)

    mangopay_user = MangoPayLegalUser()
    mangopay_user.user = user
    mangopay_user.country_of_residence = "SE"
    mangopay_user.nationality = "US"
    mangopay_user.birthday = date(1989, 10, 20)
    mangopay_user.first_name = "Rebecca"
    mangopay_user.last_name = "Meritz"
    mangopay_user.generic_business_email = "office@fundedbyme.com"
    mangopay_user.business_name = "FundedByMe AB"
    mangopay_user.type = BUSINESS
    mangopay_user.save()

    mangopay_user.create()

    mangopay_user.headquaters_address = "Regeringsgatan 29, 111 53 Stockholm"
    mangopay_user.save()

    mangopay_user.update()


`GET /users/{user_Id} <http://docs.mangopay.com/api-references/users/>`_
************************************************************************

This call is not supported directly. Infomation on the about the mangopay should
already be saved on your MangoPayUserModel in the normal case.

`POST /KYC/Documents <http://docs.mangopay.com/api-references/kyc/documents/>`_
*******************************************************************************

To create a mangopay document for a user just instantiate a
``MangoPayDocument``, save the user and type to the document, and the call
``create()``. If successfully created the document's status should be updated to
``CREATED`` and it should be assigned a ``mangopay_id``.
Once you have added all the pages you wanted to the document you
can ask for validation from mangopay via ``ask_for_validation()``. This should
change the status of the document to ``VALIDATION_ASKED``.

One business day after asking for validation you should be able to see if mangopay approved the document or not via
``get()`` which will get the updated document from mangopay. At this point it
should either have the status of ``VALIDATED`` or ``REFUSED``.

::

    from mangopay.models import MangoPayUser
    from mangopay.constants import IDENTITY_PROOF

    mangopay_user = MangoPayUser.object.get(id=1)

    mangopay_document = MangoPayDocument()
    mangopay_document.mangopay_user = mangopay_user
    mangopay_document.type = IDENTITY_PROOF
    mangopay_document.save()

    mangopay_document.create()

    # Then add a 1+ MangoPayPages to your mangopay_document

    mangopay_document.ask_for_validation()



`POST /KYC/Documents/Pages <http://docs.mangopay.com/api-references/kyc/pages/>`_
*********************************************************************************

`GET /KYC/Documents/{Document_Id} <http://docs.mangopay.com/api-references/kyc/documents/>`_
********************************************************************************************

Wallets
-------

MangoPayWallet

`POST /wallets`_

`GET /wallets/{Wallet_Id}`_

PayIns
------

MangoPayPayIn
MangoPayCard
MangoPayCardRegistration

`POST /payins/card/web`_
*********************

Supported by the web interface something done on your own via the API.

`POST /payins/card/direct`_

`POST /payins/preauthorized/direct`_

`GET /payins/{PayIn_Id}`_

`POST /cardregistration`_
*********************

`GET /cardregistration/{CardRegistration_Id}`_
*******************************************

`GET /cards/{Card_Id}`_
********************

`POST /preauthorization/card/direct`_
**********************************

`GET /preauthorization/{PreAuthorization_Id}`_
*******************************************

Refunds
-------
MangoPayRefund

`POST /transfers/{Transfer_Id}/Refund`_
`POST /payins/{PayIn_Id}/Refund`_
`GET /refunds/{Refund_Id}`_

PayOuts
-------
MangoPayBankAccount`
MangoPayPayOut`

`POST /payouts/bankwire`_
`GET /payouts/{PayOut_Id}`_