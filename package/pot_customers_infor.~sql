create or replace package pot_customers_infor is
  procedure p_get_customer_info(pi_customer_id in varchar2,
                                po_error_mg    out varchar2,
                                po_cur         out sys_refcursor);
  procedure p_save_customer_info(pi_cus_name     in ot.customers.name%type,
                                 pi_address      in ot.customers.address%type,
                                 pi_website      in ot.customers.website%type,
                                 pi_credit_limit in ot.customers.credit_limit%type,
                                 po_err_mg       out varchar2);
 procedure p_save_contact_info(pi_first_name in ot.contacts.first_name%type,
                                pi_last_name  in ot.contacts.last_name%type,
                                pi_email      in ot.contacts.email%type,
                                pi_phone      in ot.contacts.phone%type,
                                pi_cus_id     in ot.contacts.customer_id%type,
                                po_err_mg     out varchar2);                         
  procedure p_update_customer_info(pi_cus_id       in ot.customers.customer_id%type,
                                   pi_cus_name     in ot.customers.name%type,
                                   pi_address      in ot.customers.address%type,
                                   pi_website      in ot.customers.website%type,
                                   pi_credit_limit in ot.customers.credit_limit%type,
                                   po_err_mg       out varchar2);
  procedure p_update_contact(pi_contact_id in ot.contacts.contact_id%type,
                             pi_first_name in ot.contacts.first_name%type,
                             pi_last_name  in ot.contacts.last_name%type,
                             pi_email      in ot.contacts.email%type,
                             pi_phone      in ot.contacts.phone%type,
                             pi_cus_id     in ot.contacts.customer_id%type,
                             po_err_mg     out varchar2);
end pot_customers_infor;
/
create or replace package body pot_customers_infor is
  p_success                 constant varchar2(5) := '00000';
  p_other_mg                constant varchar2(5) := '00001';
  p_error_cus_exist         constant varchar2(5) := '00002';
  p_error_cus_not_exist     constant varchar2(5) := '00003';
  p_error_contact_not_exist constant varchar2(5) := '00004';
  procedure p_get_customer_info(pi_customer_id in varchar2,
                                po_error_mg    out varchar2,
                                po_cur         out sys_refcursor) is
  begin
    open po_cur for
      select c.customer_id,
             ct.first_name  first_contact_nm,
             ct.last_name   last_contact_nm,
             c.name         company_cus_nm,
             ct.email,
             ct.phone,
             c.address,
             c.website,
             c.credit_limit
        from ot.customers c
        left join ot.contacts ct
          on c.customer_id = ct.customer_id
       where c.customer_id = pi_customer_id;
    po_error_mg := 'Y';
  exception
    when others then
      po_error_mg := 'N';
  end p_get_customer_info;
  procedure p_save_customer_info(pi_cus_name     in ot.customers.name%type,
                                 pi_address      in ot.customers.address%type,
                                 pi_website      in ot.customers.website%type,
                                 pi_credit_limit in ot.customers.credit_limit%type,
                                 po_err_mg       out varchar2) is
    l_cus_exist number;
    l_max_id    number;
    l_id        number;
    cursor c_chk_cus_exist is
      select count(*) from ot.customers c where c.name = pi_cus_name;
  
    cursor c_max_id is
      select max(c.customer_id) from ot.customers c;
  begin
    open c_chk_cus_exist;
    fetch c_chk_cus_exist
      into l_cus_exist;
    close c_chk_cus_exist;
    if l_cus_exist > 0 then
      po_err_mg := p_error_cus_exist;
    else
      l_id := ot.cus_seq.nextval;
      open c_max_id;
      fetch c_max_id
        into l_max_id;
      close c_max_id;
      if l_id <= l_max_id then
        loop
          l_id := ot.cus_seq.nextval;
          exit when l_id > l_max_id;
        end loop;
      end if;
      insert into ot.customers
      values
        (l_id, pi_cus_name, pi_address, pi_website, pi_credit_limit);
      po_err_mg := p_success;
      commit;
    end if;
  Exception
    when others then
      po_err_mg := p_other_mg;
      ROLLBACK;
  end;
  procedure p_save_contact_info(pi_first_name in ot.contacts.first_name%type,
                                pi_last_name  in ot.contacts.last_name%type,
                                pi_email      in ot.contacts.email%type,
                                pi_phone      in ot.contacts.phone%type,
                                pi_cus_id     in ot.contacts.customer_id%type,
                                po_err_mg     out varchar2) is
  l_cus_exist number;
  l_max_id number;
  l_id number;
  cursor c_max_id is
   select max(c.contact_id)
   from ot.contacts c;
  cursor c_chk_cus_exist is
   select count(c.customer_id)
   from ot.customers c
   where c.customer_id = pi_cus_id;                           
  begin
    open c_chk_cus_exist;
    fetch c_chk_cus_exist into l_cus_exist;
    close c_chk_cus_exist;
    if l_cus_exist < 1 then
      po_err_mg := p_error_cus_not_exist;
    else
      l_id := ot.contact_seq.nextval;
      open c_max_id;
      fetch c_max_id into l_max_id;
      close c_max_id;
      if l_id <= l_max_id then
        loop
          l_id := ot.contact_seq.nextval;
          exit when l_id > l_max_id;
        end loop;
      end if;
      insert into ot.contacts values(l_id, pi_first_name, pi_last_name, pi_email, pi_phone, pi_cus_id);
      po_err_mg := p_success;
      commit;
    end if;
    Exception
      when others then
        po_err_mg := p_other_mg;
        ROLLBACK;
  end p_save_contact_info;                              
  procedure p_update_customer_info(pi_cus_id       in ot.customers.customer_id%type,
                                   pi_cus_name     in ot.customers.name%type,
                                   pi_address      in ot.customers.address%type,
                                   pi_website      in ot.customers.website%type,
                                   pi_credit_limit in ot.customers.credit_limit%type,
                                   po_err_mg       out varchar2) is
    l_cus_exist number;
    cursor c_chk_cus_exist is
      select count(*) from ot.customers c where c.customer_id = pi_cus_id;
  begin
    open c_chk_cus_exist;
    fetch c_chk_cus_exist
      into l_cus_exist;
    close c_chk_cus_exist;
    if l_cus_exist = 0 then
      po_err_mg := p_error_cus_not_exist;
    else
      update ot.customers c
         set c.name         = pi_cus_name,
             c.address      = pi_address,
             c.website      = pi_website,
             c.credit_limit = pi_credit_limit
       where c.customer_id = pi_cus_id;
      po_err_mg := p_success;
      commit;
    end if;
  Exception
    when others then
      po_err_mg := p_other_mg;
      ROLLBACK;
  end p_update_customer_info;
  procedure p_update_contact(pi_contact_id in ot.contacts.contact_id%type,
                             pi_first_name in ot.contacts.first_name%type,
                             pi_last_name  in ot.contacts.last_name%type,
                             pi_email      in ot.contacts.email%type,
                             pi_phone      in ot.contacts.phone%type,
                             pi_cus_id     in ot.contacts.customer_id%type,
                             po_err_mg     out varchar2) is
    l_contact_exist number;
    cursor c_chk_contact_exist is
      select count(*)
        from ot.contacts c
       where c.contact_id = pi_contact_id;
  begin
    open c_chk_contact_exist;
    fetch c_chk_contact_exist
      into l_contact_exist;
    close c_chk_contact_exist;
    if l_contact_exist = 0 then
      po_err_mg := p_error_contact_not_exist;
    else
      update ot.contacts c
         set c.first_name  = pi_first_name,
             c.last_name   = pi_last_name,
             c.email       = pi_email,
             c.phone       = pi_phone,
             c.customer_id = pi_cus_id
       where c.contact_id = pi_contact_id;
      po_err_mg := p_success;
      commit;
    end if;
  Exception
    when others then
      po_err_mg := p_other_mg;
      ROLLBACK;
  end p_update_contact;
end pot_customers_infor;
/
