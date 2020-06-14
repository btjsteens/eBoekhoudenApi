# PHP Wrapper for eBoekhouden SOAP server
This package makes it easy to connect to your eBoekhouden Accounting system.
This is forked project. Much of the work has been done by the original authors and contributors.

## Disclaimer
THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE. 

## API Documentation
The implementation is based on the E-Boekhouden SOAP documentation found here: 
https://secure.e-boekhouden.nl/handleiding/Documentatie_soap.pdf

## Setup
Login to your account on eBoekhouden and go to Beheer -> Instellingen -> API/SOAP.
 
Call the eBoekhoudenConnect class as follow:

```$eBoekhouden = new eBoekhoudenConnect("Username", "SecurityCode1", "SecurityCode2");```

## Usage
After the setup, you can use the class as described below.

### Relations
Here are some usage examples.

#### Find relation
```
$relation = $eBoekhouden->getRelationByCode('BAR');
if (isset($relation->Relaties->cRelatie)) {
    // Relation exists
}
```   
#### Add new relation
```
$relation = new Relation();
$relation->setRelationCode("BAR"); // Relation code must not exist
$relation->setCompanyName("Foo Company");
$eBoekhouden->addRelation($relation);
```   
#### Update a relation
```
$relation = new Relation();
$relation->setRelationCode("BAR"); // Relation code must exist
$relation->setCompanyName("Acme Company");
$eBoekhouden->updateRelation($relation);
```
#### Other relation methods
See Relation.php.


### Mutations
Here are some usage examples.

#### Find mutation
```
$mutation = $eBoekhouden->getMutationsByInvoiceNumber($invoiceNumber);
if (isset($mutation->Mutaties->cMutatieList)) {
    // Mutation with $invoiceNumber exists
}
```
#### Add new mutation
```
$mutation = new Mutation();
$mutation->setKind("FactuurVerstuurd"); // FactuurOntvangen, FactuurVerstuurd, FactuurbetalingOntvangen, FactuurbetalingVerstuurd, GeldOntvangen, GeldUitgegeven
$mutation->setDate(date('Y-m-d'));
$mutation->setAccount(1000); // Ledger account code (in this example, the acocunt for debtors)
$mutation->setRelationCode("BAR"); // Must match existing Relation
$mutation->setInvoiceNumber("INV-500"); // Must be unique
$mutation->setTermOfPayment(30); // In days
$mutation->setDescription("FOO");
$mutation->setInOrExVat("IN");
$mutation->addMutationLine(10, 21, "HOOG_VERK_21", 8000, 0)); // Price, VAT %, VAT tariff name (see documentation chapter 4), ledger account code for mutation and optional cost center ID.
$eBoekhouden->addMutation($mutation);
```
#### Other mutation methods
See Mutation.php.

### Invoices
Please not that you have to use the Mutation functions when synchronising orders/invoices from your own system to e-Boekhouden.nl, not Invoices.

```$eBoekhouden->getInvoices($dateFrom, $toDate, $invoiceNumber, $relationCode)```

| Field | Format | Mandatory |
| --- | --- | :---: |
| $dateFormat | yyyy-mm-dd | Y |
| $toDate | yyyy-mm-dd | Y |
| $invoiceNumber | STRING 50 | N |
| $relationCode | STRING 15 | N |

### Debug
Add this to get more debug information:
```
var_dump($this->soapClient->__getLastRequestHeaders()); // the headers of your last request
var_dump($this->soapClient->__getLastRequest()); // your last request
```
